<script context="module">
    export async function load({ params }) {
        let noise = params.noise
        let group = params.group
        return { props: {
            noise: noise,
            group: group
        }}
    }
</script>

<script>
import WhiteBtn from "$lib/components/buttons/styles/white_button.svelte";
import EnglishText from "$lib/components/english_text.svelte";
import LearnerText from "$lib/components/learner_text.svelte";
import SoundSpeaker from "$lib/components/buttons/sound_speaker.svelte"
import SoundLetter from "$lib/components/buttons/sound_letter.svelte"
export let noise;
export let group;
let audio;

const letter_groups = {
    1: ["s", "a", "t", "p", "i", "n"],
    2: ["c", "k", "e", "h", "r", "m", "d"],
    3: ["g", "o", "u", "l", "f", "b"],
    4: ["j", "z", "w", "v", "y", "x"]
}

const LETTERS = letter_groups[group]

function shuffle(original) {
    console.log(original)
    var array = [...original]
    for (var i = array.length - 1; i > 0; i--) {
        var j = Math.floor(Math.random() * (i + 1));
        var temp = array[i];
        array[i] = array[j];
        array[j] = temp;
    }
    return array;
}

var correct_letter, three_letters

function selectLetters() {
    let shuffled = shuffle(LETTERS)
    correct_letter = shuffled[0]
    let falses = shuffled.slice(1,3)
    three_letters = shuffle([...falses, correct_letter])
}

selectLetters()
let guessed_letter = ""

function reset() {
    guessed_letter = ""
    selectLetters()
}

function press(letter, i) {
    guessed_letter = letter
    let audio_source
    console.log(guessed_letter==correct_letter)
    console.log(correct_letter)
    if (guessed_letter == correct_letter) {
        audio_source = "/audio/sound_effects/correct_tone.mp3"
    } else {
        audio_source = "/audio/sound_effects/error_tone.mp3"
    }
    audio.src = audio_source
    setTimeout(() => {
        audio.play()
    }, 800);
    setTimeout(() => {
        reset()
    }, 3200);
}
</script>

<div class="outer-grid"> 
    <a href="/group/{group}" class="back-button">
        <WhiteBtn><div class="illustration"><img class="arrow" src="/images/icons/arrow_backward.svg" alt="arrow"></div></WhiteBtn>
    </a>
    <div class="learn-the-letters">
        {#if noise == "sound"}
            <div class="english">
                <EnglishText key={"match_sound"}></EnglishText>
            </div>
            <LearnerText key={"match_sound"}></LearnerText>
        {:else if noise == "name"}
            <div class="english">
                <EnglishText key={"match_name"}></EnglishText>
            </div>
            <LearnerText key={"match_name"}></LearnerText>
        {/if}
    </div>
    <div class="speaker"><SoundSpeaker {noise} letter={correct_letter}/></div> 
    {#each three_letters as ltr, i}
        <div class="letter-{i}" on:click={() => {press(ltr, i)}}>
            <SoundLetter {guessed_letter} {correct_letter} letter={ltr} />
        </div>
    {/each}
</div>

<audio
  style="display:none;"
  bind:this="{audio}"
  on:ended="{() => {}}"
  volume="1"
  controls
><track kind="captions" /></audio>

<style>
.outer-grid { 
    position: relative;

    display: grid;
    padding: 10px 12px 18px;
    grid-template-columns: 1fr 1fr 1fr; 
    grid-template-rows: 50px 230px auto auto;
    grid-gap: 24px;

    place-items: center;

    width: 100vw;
    height: 100vh;
}

.back-button {
    grid-row: 1 / 2;
    grid-column: 1 / 2;

    width: 100%;
    height: 100%;
}

.illustration {
    align-items: center;
    justify-content: center;

    display: flex;

    height: 100%;
}

.arrow {
    position: absolute;   

    height: 110%;
}

.learn-the-letters {
    grid-row: 2 / 3;
    grid-column: 1 / 4;   

    width: auto;
}

.english {
    margin-bottom: 8px;
}

.speaker {
    grid-row: 3 / 4;
    grid-column: 1 / 4;

    width: 80%;
    height: 100%;
}

.letter-0 {
    grid-row: 4 / 5;
    grid-column: 1 / 2;

    height: 100%;
    width: 100%;
}

.letter-1 {
    grid-row: 4 / 5;
    grid-column: 2 / 3;

    height: 100%;
    width: 100%;
}

.letter-2 {
    grid-row: 4 / 5;
    grid-column: 3 / 4;

    height: 100%;
    width: 100%;
}
</style>