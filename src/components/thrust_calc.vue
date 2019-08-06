<template>
    <div class="Thruster_calc_section">
        <form @submit.prevent="onSubmit">
            <p>Desired acceleration: <input v-model="desired_acceleration"> m/s^2 <span style="color:red;" v-if="isNaN(desired_acceleration)">The speed can only be a number. </span></p>
            <p>The mass of the ship without thrusters: <input v-model="ship_mass_without_thrusters"> kg <span style="color:red;" v-if="isNaN(ship_mass_without_thrusters)">The mass can only be a number.</span></p>
            <p>Select the grid type:
                <label for="small_grid_button">Small grid: </label>
                <input type="radio" id="small_grid_button" value="small_grid" v-model="thruster_class">
                <br>
                <label for="large_grid_button">Large grid: </label>
                <input type="radio" id="large_grid_button" value="large_grid" v-model="thruster_class">
            </p>
            <div v-if="thruster_class">
                <p>Select the thruster: </p>
                <div v-for="type in thruster_list" :key="type">
                    <label for="thruster_select">{{type.name}}</label>
                    <input type="radio" id="thruster_select" :value="type" v-model="selected_thruster">
                </div>
                <br>
                <div v-if="selected_thruster">
                    <p>
                        {{selected_thruster.name}}: <br>
                        Thrust provided by thruster: {{selected_thruster.thrust}}N <br>
                        Mass of the thruster: {{selected_thruster.mass}}kg
                    </p>
                </div>
            </div>
            <input type="submit" value="Compute">
            <p>
                Number of thrusters needed: {{ceiled_number_of_thrusters_needed}} <br>
                Exact number of thrusters needed: {{number_of_thrusters_needed}}
            </p>
            <p style="color:red;" v-if="sim_limit_exceeded">SIMULATION COMPLEXITY EXCEEDED, ABORTING OPERATION</p>
        </form>
    </div>
</template>

<script>
import thruster_values_json from "../assets/thruster_values.json"

export default {
    name:"ThrustCalculator",
    data(){
        return{
            //INPUT VARIABLES
            desired_acceleration:null,
            ship_mass_without_thrusters:null,
            thruster_class:null,
            thruster_list:[],
            selected_thruster:null,

            //OUTPUT VARIABLES
            number_of_thrusters_needed:null,

            //SIMULATION
            sim_limit_exceeded:false,

            //constant object of all thruster values in the game
            THRUSTER_VALUES:thruster_values_json,
        }
    },
    methods:{
        generate_thruster_list(){
            this.thruster_list=[];
            for(var type in this.THRUSTER_VALUES[this.thruster_class]){
                this.thruster_list.push(this.THRUSTER_VALUES[this.thruster_class][type])
            }
            this.selected_thruster=null;
        },
        onSubmit(){
            this.sim_limit_exceeded=false;
            var init_req_force=this.ship_mass_without_thrusters*this.desired_acceleration;
            var init_thruster_qty=init_req_force/this.selected_thruster.thrust;
            var ceil_thruster_qty=Math.ceil(init_thruster_qty);

            var total_mass=parseFloat(this.ship_mass_without_thrusters)+parseFloat(this.selected_thruster.mass*ceil_thruster_qty);
            var total_req_force=total_mass*this.desired_acceleration;
            var total_thruster_qty=total_req_force/this.selected_thruster.thrust;
            var sim_loop=0;
            while(total_thruster_qty>ceil_thruster_qty){
                ceil_thruster_qty++;
                //add effect here ;)
                total_mass=parseFloat(this.ship_mass_without_thrusters)+parseFloat(this.selected_thruster.mass*ceil_thruster_qty);
                total_req_force=total_mass*this.desired_acceleration;
                total_thruster_qty=total_req_force/this.selected_thruster.thrust;
                sim_loop++;
                console.log(sim_loop);
                if(sim_loop>3000){
                    console.log("Simulation exceded 100 operations, aborting");
                    this.sim_limit_exceeded=true;
                    break;
                }
            }
            this.number_of_thrusters_needed=total_thruster_qty;
            if(this.sim_limit_exceeded){
                this.number_of_thrusters_needed="ERR";
            }
        }
    },
    computed:{
        ceiled_number_of_thrusters_needed(){
            return Math.ceil(this.number_of_thrusters_needed);
        }
    },
    watch:{
        thruster_class:function(){
            this.generate_thruster_list()
        }
    }
}
</script>
