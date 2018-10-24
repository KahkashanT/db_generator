

<template>
<div >
    <div id="container"><h1 id="heading">Random Data Generator</h1></div>
    <entryHeader></entryHeader>
</div>
</template>

<script>
    
import  entryHeader from './baseComponent/entryHeader.vue';
export default {
  name:'App',
   components:{
    entryHeader
    
    } 
}
</script>

<style>
    
    #container{
        overflow:hidden;
        width:80%;
        margin:10px;
        align:center;
    }
    #heading{
        margin:auto;
        align:center;
        width:80%;
        margin-left: 400px;
        font-size: 40px;
    }
</style>


