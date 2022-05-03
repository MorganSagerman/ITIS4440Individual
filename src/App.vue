<script setup>
import { ref, reactive } from "vue";

import Description from "./components/Description.vue";
import About from "./components/About.vue";
import Instructions from "./components/Instructions.vue";
import Sources from "./components/Sources.vue";
import ResultsSingle from "./components/ResultsSingle.vue";
import ResultsMultiple from "./components/ResultsMultiple.vue";
import FullRoutine from "./components/FullRoutine.vue";

import exercises from "./assets/exercises.json";

// Tab variables for first tab section
const infoTab = ref("Description");
const informationTabs = {
  Description,
  Instructions,
  About,
  Sources
};

// Variables for result possibilities
const result = ref("");
const resultOptions = {
  ResultsSingle,
  ResultsMultiple,
  FullRoutine
};

// Muscle Checkbox Array
const muscleSelect = reactive([false, false, false, false, false, false]);

// Equipment Checkbox Array
const equipmentSelect = reactive([false, false, false, false, false]);

// List of Muscles
const muscleList = reactive(["Chest", "Shoulders", "Biceps", "Triceps", "Back", "Legs"]);
const equipmentList = reactive(["None", "Resistance", "Dumbbell", "Barbell", "Machine"]);

const muscleSearch = reactive([]);
const equipmentSearch = reactive([]);

const answer = reactive([]);
const empty = ref(true);

// Initial method which resets output values, ensures that at least some items are selected, and proceeds based on button selection.
function validateInput() {
  reset();
  const muscleCheck = ref(false);
  const equipmentCheck = ref(false);

  // Check that at least one muscle and at least one equipment box is ticked.
  muscleCheck.value = muscleSelect.some(function(x) {
    return x;
  });
  equipmentCheck.value = equipmentSelect.some(function(y) {
    return y;
  });

  // Method call dependent on which button was pressed
  if (muscleCheck.value && equipmentCheck.value) {
    if (result.value === "ResultsSingle") {
      findSingleResult();
    }
    else if (result.value === "ResultsMultiple") {
      findMultipleResults();
    }
    else if (result.value === "FullRoutine") {
      generateFullRoutine();
    }
  }
  else {
    alert("Please select at least one option from each category.");
  }
}

// Resets the array values to be reused for each call
function reset() {
  answer.splice(0, answer.length);
  muscleSearch.splice(0, muscleSearch.length);
  equipmentSearch.splice(0, equipmentSearch.length);
};

// Used to create arrays of what specific muscle to search for, comparable by name instead of boolean value
function initializeSearch() {
  // Creates an array of what muscles to search for in the database.
  muscleList.forEach((item, x) => {
    if(muscleSelect[x]) {
      muscleSearch.push(muscleList[x]);
    }
  });

  // Creates an array of what equipment to search for in the database.
  equipmentList.forEach((item, x) => {
    if(equipmentSelect[x]) {
      equipmentSearch.push(equipmentList[x]);
    }
  });
};

function finalize(final) {
  // Clones the parameter array into the answer array.
  if (final.length > 0) {
    empty.value = false;
    answer.push(...final);
  }
  else {
    empty.value = true;
  }
}

// This method will output one exercise into the 'answer' array.
function findSingleResult() {
  const possibleExercisesMuscle = reactive([]);
  const possibleExercisesEquipment = reactive([]);

  const finalPool = reactive([]);

  initializeSearch();

  // Collects complete exercises that fulfill the muscle filters.
  exercises.results.forEach((ex) => {
    const valid = reactive([]);
    muscleSearch.forEach((ms) => {
      valid.push(ex.musclesWorked.includes(ms));
    });

    if (valid.every(function(x) { return x; })) {
        possibleExercisesMuscle.push(ex);
    }
  });

  // Collects exercises by id that fulfill the equipment filters.
  exercises.results.forEach((ex) => {
    equipmentSearch.forEach((eq) => {
      if (ex.equipment.includes(eq)) {
        possibleExercisesEquipment.push(ex.id);
      }
    });
  });

  // Compares the above two results and pushes the exercise into the final pool if a match is found.
  possibleExercisesMuscle.forEach((ex) => {
    if (possibleExercisesEquipment.includes(ex.id)) {
      finalPool.push(ex);
    }
  });
    
  finalize(finalPool);
};

// This method will output all matching exercises into the 'answer' array.
function findMultipleResults() {
  const possibleExercisesMuscle = reactive([]);
  const possibleExercisesEquipment = reactive([]);

  const finalPool = reactive([]);

  initializeSearch();

  // Collects complete exercises that fulfill the muscle filters.
  exercises.results.forEach((ex) => {
    const valid = reactive([]);
    muscleSearch.forEach((ms) => {
      valid.push(ex.musclesWorked.includes(ms));
    });

    if (valid.every(function(x) { return x; })) {
        possibleExercisesMuscle.push(ex);
    }
  });

  // Collects exercises by id that fulfill the equipment filters.
  exercises.results.forEach((ex) => {
    equipmentSearch.forEach((eq) => {
      if (ex.equipment.includes(eq)) {
        possibleExercisesEquipment.push(ex.id);
      }
    });
  });

  // Compares the above two results and pushes the exercise into the final pool if a match is found.
  possibleExercisesMuscle.forEach((ex) => {
    if (possibleExercisesEquipment.includes(ex.id)) {
      finalPool.push(ex);
    }
  });
    
  finalize(finalPool);
};

function generateFullRoutine() {
  const finalPool = reactive([]);

  initializeSearch();

  // Adds exercises to the final pool where the main muscle worked matches the user's filter
  muscleSearch.forEach((ms) => {
    const limiter = ref(0);
    exercises.results.forEach((ex) => {
      if (limiter.value < 2) {
        if (ex.mainMuscle === ms) {
          // Syntax for the below if condition (using .some() and .includes() in tandem) found at https://www.geeksforgeeks.org/how-to-find-if-two-arrays-contain-any-common-item-in-javascript/
          if (ex.equipment.some(l => equipmentSearch.includes(l))) {
            finalPool.push(ex);
            limiter.value++;
          }
        }
      }
    })
  });

  finalize(finalPool);
};
</script>

<template>
  <div>
    <header>
      <h1>Lazy Train</h1>
    </header>

    <main>
      <br>
      <h2>Welcome!</h2>
      <p>
        Hello and welcome to Lazy Train, a platform for you to quickly and easily generate a workout routine. Through a number of settings, you have control of the intensity, targets, and accessibility of your routine!
      </p>

      <!-- Tabs logic and syntax found from vuejs.org's "Dynamic Components" playground: https://vuejs.org/guide/essentials/component-basics.html#dynamic-components -->
      <div id="infoBlurb" class="blurbs">
        <button v-for="(x, info) in informationTabs" :key="info" :class="['tab', { active: infoTab === info }]" @click="infoTab = info">{{ info }}</button>

        <keep-alive>
          <component :is="informationTabs[infoTab]"></component>
        </keep-alive>
      </div>

      <div id="settingsHeader">
        <h2 id="settingsTitle" class="title">Settings</h2>
      </div>

      <div id="settings">
        <div class="settingsCol">
          <h3>Muscle</h3>
          <br>
          <input type="checkbox" id="boxChest" v-model="muscleSelect[0]">
          <label for="boxChest">  Chest  </label>
          <br>
          <input type="checkbox" id="boxShoulders" v-model="muscleSelect[1]">
          <label for="boxShoulders">  Shoulders  </label>
          <br>
          <input type="checkbox" id="boxBiceps" v-model="muscleSelect[2]">
          <label for="boxBiceps">  Biceps  </label>
          <br>
          <input type="checkbox" id="boxTriceps" v-model="muscleSelect[3]">
          <label for="boxTriceps">  Triceps  </label>
          <br>
          <input type="checkbox" id="boxBack" v-model="muscleSelect[4]">
          <label for="boxBack">  Back  </label>
          <br>
          <input type="checkbox" id="boxLegs" v-model="muscleSelect[5]">
          <label for="boxLegs">  Legs  </label>
        </div>
        <div class="settingsCol">
          <h3>Equipment</h3>
          <br>
          <input type="checkbox" id="boxNone" v-model="equipmentSelect[0]">
          <label for="boxNone">  None  </label>
          <br>
          <input type="checkbox" id="boxResistance" v-model="equipmentSelect[1]">
          <label for="boxResistance">  Resistance Bands  </label>
          <br>
          <input type="checkbox" id="boxDumbbell" v-model="equipmentSelect[2]">
          <label for="boxDumbbell">  Dumbbell  </label>
          <br>
          <input type="checkbox" id="boxBarbell" v-model="equipmentSelect[3]">
          <label for="boxBarbell">  Barbell  </label>
          <br>
          <input type="checkbox" id="boxMachine" v-model="equipmentSelect[4]">
          <label for="boxMachine">  Machine  </label>
        </div>
      </div>

      <br><br>
      <div id="settings">
        <button class="settingsButton" @click="result = 'ResultsSingle'; validateInput()">Single Result</button>
        <button class="settingsButton" @click="result = 'ResultsMultiple'; validateInput()">Workout List</button>
        <button class="settingsButton" @click="result = 'FullRoutine'; validateInput()">Generate  Routine</button>
      </div>
      <br>
      <div id="results">
        <keep-alive>
          <component :is="resultOptions[result]" :answer="answer" :empty="empty"></component>
        </keep-alive>
      </div>
      <br><br>
    </main>

    <footer>
      Written and designed by Morgan Sagerman for UNCC's ITIS 4440 course.
    </footer>
  </div>
</template>

<style>
:root {
  --background: #e1e1e1;
  --mainHighlight: #708090;
  --alternateHighlight: #bed2e6;
  --highlightText: #ffdab9;
  --pseudoWhite: #f5fffa;
}

#app {
  margin: 0 auto;
  padding: 0;

  font-weight: normal;
}

/* General Structure */
body {
  font-family: "Rockwell", serif;
  margin: 0;

  background-color: var(--background);
}

header {
  background-color: var(--mainHighlight);
  color: var(--highlightText);

  justify-content: center;
  align-items: center;

  width: 100%;
  
  display: flex;
}

main {
  padding-left: 10%;
  padding-right: 10%;
}

footer {
  background-color: var(--mainHighlight);
  color: var(--highlightText);

  justify-content: center;
  align-items: center;

  width: 100%;

  padding-top: 0.5em;
  padding-bottom: 0.5em;

  display: flex;
}

/* Element Styling */
p {
  text-indent: 2em;
}

a {
  text-decoration: none;
  color: var(--highlightText);
}

h3 {
  margin: 0 1em;
}

/* Class Styling */
.tab {
  background-color: var(--alternateHighlight);
  padding: 0.5em 1em;

  border-top-left-radius: 0.5em;
  border-top-right-radius: 0.5em;
  
  border-style: solid;
  border-width: 0;

  margin-right: 0.5em;
}

.tab.active {
  background-color: var(--mainHighlight);
  color: var(--pseudoWhite);
}

.tab:hover {
  background-color: var(--mainHighlight);
  color: var(--pseudoWhite);
  cursor: pointer;
}

.blurbs {
  padding: 1em 0;
}

.title {
  background-color: var(--mainHighlight);
  color: var(--highlightText);
  padding: 0 2em;

  white-space: nowrap;
  font-weight: bold;

  border-radius: 0.5em;
  border-style: solid;
  border-width: 0;
}

.italics {
  font-style: italic;
}

.bold {
  font-weight: bold;
}

.settingsCol {
  flex: 50%;
}

.settingsButton {
  margin: 0 auto;
  background-color: var(--alternateHighlight);
  padding: 0.5em 1em;

  border-radius: 0.5em;
  
  border-style: solid;
  border-width: 0;
}

.settingsButton:hover {
  background-color: var(--mainHighlight);
  color: var(--pseudoWhite);
  cursor: pointer;
}

.resultsCard span{
  margin: 0 auto;
  background-color: var(--alternateHighlight);

  display: flex;
  padding: 0.5em 10%;
}

.resultsCard h3 {
  margin: 0 auto;
  background-color: var(--mainHighlight);
  color: var(--highlightText);

  border-top-left-radius: 0.5em;
  border-top-right-radius: 0.5em;

  padding: 0.5em 1em;
}

.resultsCard footer {
  border-bottom-left-radius: 0.5em;
  border-bottom-right-radius: 0.5em;
}

/* Specific Elements */
#sources {
  margin: 0 auto;
  padding: 1em 0;

  border-top-right-radius: 1em;
  border-bottom-left-radius: 1em;
  border-bottom-right-radius: 1em;

  background-color: var(--mainHighlight);
  color: var(--pseudoWhite);
}

#results {
  margin: 0 auto;
  display: flex;
  
  justify-content: center;

  padding: 1em 0;
}

#settingsHeader {
  margin: auto;
  text-align: center;
  width: 80%;
}

#settingsTitle {
  padding: 0.25em 20%;
}

#settings {
  margin: 0 auto;
  display: flex;

  width: 50%;
}
</style>