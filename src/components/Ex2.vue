<script>
import { remove } from '@vue/shared';

    export default { 
        // Add code in appropriate places
        data() {
            return {
                newItem:'',
                items: ['keyboard', 'mouse', 'iPhone', 'macbook', 'adapter']
            }
        },
        methods:{
            removeItem(index){
                this.items.splice(index, 1)
            },
            addItem(){
                const item = this.newItem.trim()
                if (item !== ""){
                    this.items.push(item);
                    this.newItem = "";
                }
            }
        }
    }
</script>

<template>

    <h2>Shopping Cart</h2>
    <!-- TODO: Add Code Here -->
    <ul>
        <li v-for="(item, index) in items">
            {{ item }}
            <button @click="removeItem(index)">Delete!</button>
        </li>

    </ul>

    <input type="text" v-model="newItem">
        <button @click="addItem">Add!</button>
    </input>
   
</template>
