<script lang="ts">
  import { nMap } from "../../data/chordmap.ts";

  let midi;
  let chord = "Unknown";

  let notesPressed: number[] = [];

  const nC = 0;
  const nCs = 1;
  const nD = 2;
  const nDs = 3;
  const nE = 4;
  const nF = 5;
  const nFs = 6;
  const nG = 7;
  const nGs = 8;
  const nA = 9;
  const nAs = 10;
  const nB = 11;

  let dataList: Array<Uint8Array> = [];

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

      chord = toChord(notesPressed);
    }
  }

  // on failure
  function onMIDIFailure() {
    console.warn("Not recognising MIDI controller");
  }

  function toChord(notes: number[]) {
    const chordIndex = notes.sort().map(toKeyIndexPartial).join("");
    const chord = nMap[chordIndex];
    return chord ? chord : "Unknown";
  }

  function toKeyIndexPartial(midiNote: number) {
    const keyIndexPartials = [
      "0",
      "1",
      "2",
      "3",
      "4",
      "5",
      "6",
      "7",
      "8",
      "9",
      "A",
      "B",
    ];
    return keyIndexPartials[midiNote % 12];
  }
</script>

<div class="midi-wrapper">
  <div>
    <h2>Simple MIDI API Example</h2>

    <p>Plug in your MIDI controller and see the data logged here</p>

    <section id="midi-data">
      {chord}
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
    width: 90%;
    max-width: 640px;
    margin: 0px auto;
    padding: 2em;
    box-sizing: border-box;
    background: var(--cream);
    font-family: "Varela Round", sans-serif;
    color: var(--brown);
    line-height: 1.3;
  }
  .midi-wrapper > div {
    padding: 2em;
    background: transparentize(white, 0.3);
    border: 10px solid white;
  }
  h2,
  p,
  ul {
    padding-bottom: 1em;
  }
  h2 {
    text-align: center;
    font-size: 2em;
  }
  p {
    font-size: 1.2em;
  }

  #midi-data {
    position: relative;
    overflow: hidden;
    height: 300px;
    background: white;
    border: 10px solid var(--purple);
  }
  #midi-data ul {
    position: absolute;
    left: 0px;
    bottom: 0px;
    padding: 1em;
  }
  #midi-data ul li {
    padding: 0.2em 0.2em 0.2em 1em;
    font-size: 1.6em;
    font-family: monospace;
  }
  #midi-data ul li:last-child {
    font-weight: bold;
    color: var(--purple);
    animation: flash 0.5s;
  }
  #midi-data ul li:last-child:before {
    content: "> ";
    margin-left: -1em;
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
