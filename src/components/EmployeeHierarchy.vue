<script setup>
    import Employee from './Employee.vue';
    import {ref} from 'vue';
    const props = defineProps(['msg']);
    var all_employees = props.msg.split('\n');
    const cols = all_employees[0].split(",");
    const cols_index = {};
    for (var index in cols) {
        cols_index[cols[index].trim()] = parseInt(index);
    }
    all_employees = all_employees.slice(1);
    var layer_index = cols_index['level'];
    var location_index = cols_index['Location'];
    if (location_index < layer_index) {
        layer_index += 1;
    }
    function for_sorting(e1, e2) {
        let e1_vals = e1.split(",");
        let e2_vals = e2.split(",");
        return parseInt(e1_vals[layer_index]) - parseInt(e2_vals[layer_index]);
    }
    all_employees.sort(for_sorting);

    // Sort by layer later if required.
    const employee_hierarchy_ids = {};
    const employee_costs = {}; // {"" : [manager_cost, ic_cost]}
    const employee_salary = {};
    const employee_count = {};
    const divs = [];
    var layer = "";
    var visibility_arr = {};
    var buttonTexts = {};
    for (var i in all_employees) {
        var index = all_employees.length - i - 1;
        var employee = all_employees[index].split(',');
        var employee_first = employee.slice(0, cols_index['Location']);
        employee_first.push(employee[cols_index['Location']] + "," + employee[cols_index['Location'] + 1]);
        var employee_second = employee.slice(cols_index['Location'] + 2);
        employee_first.push(...employee_second);
        employee = employee_first;
        var id = employee[cols_index['Employee Id']];
        var manager = employee[cols_index['Manager']];
        var salary = parseFloat(employee[cols_index['Salary']]);
        var new_layer = employee[cols_index['level']].trim("\r");
        employee_salary[id] = salary;
        if (layer != new_layer) {
            divs.push({id: new_layer, class: new_layer, content:[]});
            layer = new_layer;
        }
        
        // If Id not in dictionary then, they have no descendants
        if (!employee_hierarchy_ids.hasOwnProperty(id)) {
            employee_hierarchy_ids[id] = [];
            employee_costs[id] = [0, 0];
            employee_count[id] = [0, 0];
        } 
        // If manager not in dictionary then, this is their first descendant
        if (manager != "" && !employee_hierarchy_ids.hasOwnProperty(manager)) {
            employee_hierarchy_ids[manager] = [id];
            // Check if descendant has more descendants
            var poss_desc = employee_hierarchy_ids[id];
            
            if (poss_desc.length == 0) {
                employee_costs[manager] = [0, employee_salary[id]];
                employee_count[manager] = [0, 1];
            } else {
                employee_count[manager] = [employee_count[id][0] + 1, employee_count[id][1]];
                employee_costs[manager] = [employee_costs[id][0] + employee_salary[id], employee_costs[id][1]];
            }
        }
        // If manager already in dictionary
        else if (employee_hierarchy_ids.hasOwnProperty(manager)) {
            employee_hierarchy_ids[manager].push(id);
            // Check if descendant has more descendants
            var poss_desc = employee_hierarchy_ids[id];
            if (poss_desc.length == 0) {
                employee_costs[manager][1] += employee_salary[id];
                employee_count[manager][1] += 1;
            } else {
                employee_count[manager][0] += employee_count[id][0] + 1;
                employee_count[manager][1] += employee_count[id][1];
                employee_costs[manager][0] += employee_costs[id][0] + employee_salary[id];
                employee_costs[manager][1] += employee_costs[id][1];
            }
        }

        divs.forEach(div => {
            if (div.id == new_layer) {
                if (new_layer == 1) {
                    visibility_arr[id] = ref(true);
                } else {
                    visibility_arr[id] = ref(false);
                }
                buttonTexts[id] = ref("Show");
                div.content.push({component: Employee, props: { employee_id: id, name: employee[cols_index['Name']], job_title: employee[cols_index['Job Title']], 
                    location: employee[cols_index['Location']], layer: employee[cols_index['level']], photo: employee[cols_index['Photo']],
                    manager_count: employee_count[id][0], manager_cost: employee_costs[id][0], ic_count: employee_count[id][1],
                    ic_cost: employee_costs[id][1]}});
            }
        });
    }
    divs.reverse();

    function hide_or_show(id) {
        var children = employee_hierarchy_ids[id];
        if (buttonTexts[id].value == "Show") {
            children.forEach(child => {
                visibility_arr[child].value = true;
                buttonTexts[child].value = "Show";
            });
            buttonTexts[id].value = "Hide";
        } else {
            children.forEach(child => {
                buttonTexts[child].value = "Hide";
                hide_or_show(child);
                visibility_arr[child].value = false;
            });
            buttonTexts[id].value = "Show";
        }
    }
</script>

<template>

    <div v-for="div in divs" :key="div.id" class="p-4 mx-4 w-full justify-center text-center align-top gap-4 whitespace-nowrap overflow-x-auto">
        <component 
            v-for="(item, index) in div.content"
            :key= "index"
            :is="item.component"
            v-bind="item.props"
            :hideShow="hide_or_show"
            :isVisible="visibility_arr[item.props.employee_id].value"
            :buttonText="buttonTexts[item.props.employee_id].value">
        </component>
    </div>
</template>

<style scoped>
</style>

