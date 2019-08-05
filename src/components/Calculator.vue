<template>
    <div>
        <p>Desired acceleration:&emsp;&emsp;&emsp;<input v-model="des_accel">   <span style="color:red" v-if="isNaN(des_accel)">&emsp; GET THE FUCK OUT</span> </p>
    </div>
</template>

<script>
export default {
    name: "Calculator",
    data(){
        return{
            des_accel:null
        }
    }
}
</script>
