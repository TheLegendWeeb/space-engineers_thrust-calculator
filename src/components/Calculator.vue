<template>
    <div>
        <form @submit.prevent="onSubmit">
            <p>Desired acceleration:&emsp;&emsp;&emsp;<input v-model="des_accel">m/s   <span style="color:red" v-if="isNaN(des_accel)">&emsp; Only numbers are allowed.</span> </p>
            <p>Ship mass (no thrusters):&emsp;&nbsp;<input v-model="ship_mass_no_thrusters">kg <span style="color:red" v-if="isNaN(ship_mass_no_thrusters)">&emsp; Only numbers are allowed.</span>  </p>
            <p>Select Grid: &emsp;
                <label for="small_grid_button">Small grid: </label>
                <input type="radio" id="small_grid_button" name="grid_type" value="small_grid" v-model="Ttype">
                <br>
                <label style="padding-left:105px;" for="large_grid_button">Large grid:</label>
                <input type="radio" id="large_grid_button" name="grid_type" value="large_grid" v-model="Ttype">
            </p>
            <div v-if="Ttype">
                <p>Select Thruster: </p>
                <!-- v-for="name in names_list" v-bind:key="name" -->
                <div v-for="type in thrusters_list" v-bind:key="type">
                    <label for="thruster_select">{{type.name}}</label>
                    <input type="radio" id="thruster_select" name="thruster_type" v-bind:value=type v-model="thruster">
                </div>
                <br>
                <div v-if="thruster">
                    <p>
                        {{thruster.name}}:<br>
                        Thrust provided by thruster: {{thruster.thrust}}N <br>
                        Mass of the thruster: {{thruster.mass}}kg
                    </p>
                    <br>
                    <!-- <input type="submit" value="Submit"> -->
                </div>
            </div>
            <input type="submit" value="Calculate">
            <!-- <input v-if="thruster" type="submit" value="Calculate"> -->
            <p>
                Nr. of thrusters needed: {{ceil_nr_thrust_needed}} <br>
                Exact nr. of thrusters needed: {{exact_nr_thrust_needed}}
            </p>
        </form>
    </div>
</template>

<script>
import thruster_values_json from "../assets/thrusters_values.json"

export default {
    name: "Calculator",
    data(){
        return{
            des_accel:null,
            ship_mass_no_thrusters:null,
            Ttype:null,
            thruster_values: thruster_values_json,
            thrusters_list:[],
            thruster:null,
            // OUTPUT
            exact_nr_thrust_needed:null,
            ceil_nr_thrust_needed:null,
        }
    },
    methods:{
        update_thruster_list:function(){
            var thrusters=[];
            for(var type in this.thruster_values[this.Ttype]){
                thrusters.push(this.thruster_values[this.Ttype][type]);
            }
            this.thrusters_list=thrusters;
            this.thruster=null;
        },
        onSubmit(){
            var force_req=this.ship_mass_no_thrusters*this.des_accel;
            var thrust_qty=force_req/this.thruster.thrust;
            var ceil_thrust_qty=Math.ceil(thrust_qty);
            
            var total_mass=parseFloat(this.ship_mass_no_thrusters)+parseFloat((this.thruster.mass*ceil_thrust_qty));
            var total_force_req=total_mass*this.des_accel;
            var total_thrust_qty=total_force_req/this.thruster.thrust;
            var i=0;
            while(total_thrust_qty>ceil_thrust_qty){
                ceil_thrust_qty++;
                //add efect here ;)
                total_mass=parseFloat(this.ship_mass_no_thrusters)+parseFloat((this.thruster.mass*ceil_thrust_qty));
                total_force_req=total_mass*this.des_accel;
                total_thrust_qty=total_force_req/this.thruster.thrust;
                i++;
                if(i>10){break;
                console.log("fuk");}
            }
            this.exact_nr_thrust_needed=total_thrust_qty;
            this.ceil_nr_thrust_needed=ceil_thrust_qty;
        }
    },
    watch:{
        Ttype:function(){
            this.update_thruster_list()
        }
    }
}
</script>
<style>

</style>
