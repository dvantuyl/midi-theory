<script lang="ts">
  import { Note, Chord } from "tonal";

  let midi;

  let notesPressed: number[] = [];
  let notes: string[] = [];
  let chords: string[] = [];

  // // start talking to MIDI controller
  if (navigator.requestMIDIAccess) {
    navigator
      .requestMIDIAccess({
        sysex: false,
      })
      .then(onMIDISuccess, onMIDIFailure);
  } else {
    console.warn("No MIDI support in your browser");
  }

  // on success
  /**
   * @param {any} midiData
   */
  function onMIDISuccess(midiData: MIDIAccess) {
    // this is all our MIDI data
    midi = midiData;
    let allInputs = midi.inputs.values();
    // loop over all available inputs and listen for any MIDI input
    for (
      let input = allInputs.next();
      input && !input.done;
      input = allInputs.next()
    ) {
      // when a MIDI value is received call the onMIDIMessage function
      input.value.onmidimessage = gotMIDImessage;
    }
  }

  /**
   * @param {{ data: string; }} messageData
   */
  function gotMIDImessage(messageData: Partial<MIDIMessageEvent>) {
    if (messageData.data && messageData.data[1]) {
      if (messageData.data[0] === 144) {
        notesPressed = [...notesPressed, messageData.data[1]];
      } else if (messageData.data[0] === 128) {
        notesPressed = notesPressed.filter(
          (note) => note !== messageData.data[1]
        );
      }

      notes = notesPressed.sort().map(Note.fromMidi);
      chords = Chord.detect(notes);
    }
  }

  // on failure
  function onMIDIFailure() {
    console.warn("Not recognising MIDI controller");
  }

  function chordLookup(chord: string) {
    return Chord.get(chord)?.name;
  }
</script>

<div class="midi-wrapper">
  <div>
    <section id="chord-data">
      {#each chords as chord}
        <h2>{chordLookup(chord) || chord}</h2>
      {/each}
    </section>
    <section id="node-data">
      {#each notes as note}
        <div>{note}</div>
      {/each}
    </section>
  </div>
</div>

<style>
  :root {
    --cream: hsla(33, 50%, 90%, 1);
    --brown: hsla(340, 30%, 30%, 1);
    --purple: hsla(310, 30%, 40%, 1);
    --green: hsla(52, 75%, 45%, 1);
  }
  .midi-wrapper {
    width: 100%;
    margin: 0px auto;
    box-sizing: border-box;
    font-family: "Varela Round", sans-serif;
    color: var(--brown);
  }

  #chord-data {
    position: relative;
  }
  #chord-data h2 {
    font-size: 6em;
    font-family: monospace;
  }
  #chord-data h2 {
    font-weight: bold;
    color: var(--purple);
    animation: flash 0.5s;
  }

  #node-data {
    position: absolute;
    bottom: 1em;
    left: 0;
    width: 100%;
    display: flex;
    justify-content: center;
    gap: 1em;
    padding: 0.2em 0.2em 0.2em 1em;
    font-size: 3em;
    font-family: monospace;
    color: var(--purple);
    animation: flash 0.5s;
  }

  @keyframes flash {
    0% {
      color: var(--green);
    }
    100% {
      color: var(--purple);
    }
  }
</style>
