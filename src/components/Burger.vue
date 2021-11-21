<template>


<div class="box">
  <h3>{{ burger.name }}</h3>
  <img v-bind:src="burger.url">
  <p> Amount: {{amountOrdered}}
  <span>
  <button type="button" v-on:click="addBurger">+</button></span>
  <span><button type="button" v-on:click="removeBurger">-</button></span>
  </p>
  <ul>
  <li>{{burger.kCal+' kCal'}}</li>

  <li v-if=burger.gluten>
      Contains <span class="ingrediens"> gluten</span>
  </li>
  <li v-if=burger.lactose>
      Contains <span class="ingrediens"> lactose</span>

  </li>

</ul>
</div>

</template>

<script>
export default {
  name: 'Burger',
  props: {
    burger: Object
  },
  data: function () {
  return {
    amountOrdered: 0,
  }
},
methods: {
addBurger:function(){
this.amountOrdered += 1;
this.$emit('orderedBurger', { name:   this.burger.name,
                                amount: this.amountOrdered
                              }
  );
},

removeBurger:function(){
if(this.amountOrdered==0){
return
}
else{
  this.amountOrdered -=1;
  this.$emit('orderedBurger', { name:   this.burger.name,
                                  amount: this.amountOrdered
                                }
    );
    }
}



}
}

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

button{
padding: 2px 2px 2px 2px;
margin:1px;
border: 2em black;
border-radius: 2px;
width:1.5em;
height:1.5em;
}
button:hover{
background-color:darkgrey;
}



</style>
