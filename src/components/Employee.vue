<script setup>   
    import EmployeeHierarchy from './EmployeeHierarchy.vue';
    const props = defineProps({
    name: String, job_title: String, location: String, layer: String, photo: String, 
    manager_count: Number, manager_cost: Number, ic_cost: Number, ic_count: Number, employee_id: String,
    isVisible: {type: Boolean, required: true}, hideShow: {type: Function, required: true},
    buttonText: {type: String, required: true}
    });

    // Convert number to million, billion or so on.
    function format_number (num) {
        // Billion
        let new_num = num;
        if (num >= 1000000000) {
            new_num = "$" + (num / 1000000000).toFixed(2) + "B";
        } 
        // Million
        else if (num >= 1000000) {
            new_num = "$" + (num / 1000000).toFixed(2) + "M";
        } 
        // Thousand
        else if (num >= 1000) {
            new_num = "$" + (num / 1000).toFixed(2) + "K";
        } else {
            new_num = "$" + num.toFixed(2);
        }
        return new_num;
    }

    var new_manager_cost = format_number(props.manager_cost);
    var new_ic_cost = format_number(props.ic_cost);
    var total_cost = format_number(props.manager_cost + props.ic_cost);
    var manager_ic_ratio = (props.manager_count / props.ic_count).toFixed(2);
    var manager_cost_ratio = (props.manager_cost / (props.manager_cost + props.ic_cost)).toFixed(2);
    var new_location = props.location.replace('"', '').replace('"', '');
</script>

<template>
    <div id="props.employee_id" v-if="isVisible" class="inline-block p-8 bg-sky-200 mx-4 rounded-3xl items-center justify-center text-center align-center">
        <img :src='props.photo'/>
        <h3 class="text-3xl font-extrabold">{{ name }}</h3>
        <h4 class="text-2xl dark:text-white rounded-full m-1 p-2">{{ job_title }}</h4>
        <h5 class="text-xl dark:text-white bg-gray-300 rounded-full m-1 p-2">{{ new_location }}</h5>
        <h5 class="text-xl dark:text-white bg-gray-300 rounded-full m-1 p-2">Layer: {{ layer }}</h5>
        <h5 class="text-xl dark:text-white bg-gray-300 rounded-full m-1 p-2">Manager Count: {{ manager_count }}</h5>
        <h5 class="text-xl dark:text-white bg-gray-300 rounded-full m-1 p-2">Manager Cost: {{ new_manager_cost }}</h5>
        <h5 class="text-xl dark:text-white bg-gray-300 rounded-full m-1 p-2">IC Count: {{ ic_count }}</h5>
        <h5 class="text-xl dark:text-white bg-gray-300 rounded-full m-1 p-2">IC Cost: {{ new_ic_cost }}</h5>
        <h5 class="text-xl dark:text-white bg-gray-300 rounded-full m-1 p-2">Total Cost: {{ total_cost }}</h5>
        <h5 class="text-xl dark:text-white bg-gray-300 rounded-full m-1 p-2">Manager Cost Ratio: {{ manager_cost_ratio }}</h5>
        <h5 class="text-xl font-bold dark:text-white bg-gray-300 rounded-full m-1 p-2">Manager IC Ratio: {{ manager_ic_ratio }}</h5>
        <button @click="props.hideShow(props.employee_id);" class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded-full">
            {{ buttonText }}</button>
    </div>


</template>

<style scoped>

    h4, h5 {
        width: fit-content;
    }

</style>