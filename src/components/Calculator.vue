<template>
    <div>
        <p>Desired acceleration:&emsp;&emsp;&emsp;<input v-model="des_accel">m/s   <span style="color:red" v-if="isNaN(des_accel)">&emsp; Only numbers are allowed.</span> </p>
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
<style>

</style>
