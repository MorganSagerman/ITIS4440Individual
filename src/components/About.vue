<template>
    <div>
        <p>
            Lazy Train is an application made by Morgan Sagerman for the Spring 2022 iteration if the ITIS 4440 class, section 001.
        </p>
        <p>
            This application makes use of version <span class="bold">3.2.33</span> of the Vue.js framework, and does not use any additional software or languages.
            I have opted to use the composition API over the options API, for ease of access and familiarity.
        </p>
        <p>
            Not all of the code in the application was writtten or designed by me; the tabs feature (which you have used if you are reading this) comes from the vuejs.org tutorial on components.
            All 3rd party code is credited both in the source code of this application and in the following sources tab.
        </p>
        <p>
            This application is not entirely complete; there are a number of improvements which could be made. These include, but are not limited to:
        </p>
        <ol style="margin-left: 3em">
            <li>The results cards could be improved aesthetically.</li>
            <li>The routine generation is somewhat lackluster and could have more functionality, such as number of sets/reps or difficulty.</li>
            <li>The routine generation is not randomized; it will only select the first couple of exercises of each selected muscle.</li>
        </ol>
    </div>
</template>