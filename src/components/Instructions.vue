<template>
    <div>
        <p>
            This application has 3 major functions, all of which require you select at least one target muscle and available equipment type.
            The application operates by selecting workouts that target all selected muscles, but will look for any selected equipment.
            Thus far, the 3 available functions are:
        </p>
        <ol style="margin-left: 3em">
            <li>Generate a single workout.</li>
            <li>Generate a list of workouts.</li>
            <li>Generate a simple routine.</li>
        </ol>
        <p>
            First, generating a single workout will output (if found) one exercise that meets your parameters.
            Second, the workout list displays all exercises that adhere to the filters, not just one.
            Finally, the generate routine function will display <span class="bold">at most</span> 2 exercises for <span class="italics">each</span> selected muscle, performable using at least one selected equipment type, which primarily targets that muscle.
        </p>
    </div>
</template>