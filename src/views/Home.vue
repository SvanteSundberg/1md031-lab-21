<template>

  <header>

     <img id="logo" class="headline" src="img/flames.jpg">
     <h1 id="headline"> Welcome to BurgerWorld! </h1>
  </header>
  <main>
    <section id="burgers">
      <h2> What do you want </h2>
         <p> What is your choice of bruger? </p>
     <div class="wrapper">
     <Burger v-for="burger in burgers"
             v-bind:burger="burger"
             v-bind:key="burger.name"
             v-on:orderedBurger="addToOrder($event)"/>
     </div>

     </section>

     <section id="order" style= "clear:left;">
       <h2> Customer info </h2>
         <p> Fill in your information </p>

     <h4> Delivery info</h4>
     <form>
     <p>
       <label for="name">Full name:</label><br>
       <input type="text" id="name" v-model="fn" required="required" placeholder="First- and last name">
     </p>

     <p>
       <label for="email">Email adress:</label><br>
       <input type="email" id="email" v-model="em" required="required" placeholder="E-mail address">
     </p>
<!--
     <p>
       <label for="street">Street:</label><br>
       <input type="text" id="street" v-model="street" required="required" placeholder="Street adress">
       Street is:{{street}}
     </p>

     <p>
       <label for="housenumber">House:</label><br>
       <input type="number" id="housenumber" v-model="house" required="required" placeholder="House number">
       House number is: {{house}}
     </p>
-->
     <p>
       <label for="payment">Payment:</label><br>
         <select id="payment" v-model="payment">
           <option>Debit</option>
           <option>Kredit</option>
           <option selected="selected">Cash</option>
           <option>Swish</option>
         </select>

     </p>
     <p>Select gender:</p>
     <p>
       <input type="radio" id="male" value="male" v-model="gender"  placeholder="Male">
       <label for="male">Male</label>
     </p>
     <p>
       <input type="radio" id="female" value="female" v-model="gender"  placeholder="Female">
       <label for="female">Female</label>
     </p>
     <p>
       <input type="radio" id="other" value="other" v-model="gender" placeholder="Other">
       <label for="other">Other</label>
     </p>

   </form>
   <p> Select point of delivery: </p>
   <div id="mapBox">
   <div id="map" v-on:click="setLocation" >
    <div  v-bind:style="{ left: this.location.x + 'px',
                      top: this.location.y + 'px' }">
    T

   </div>
      </div>
   </div>
     </section>
     <button id="orderButton" v-on:click="markDone(key)" type="submit">
       <img src="img/smiley.jpg" style= "width: 30px;">
       Order!
     </button>
   </main>
   <hr>
   <footer>
       &copy; svante inc
     </footer>


</template>

<script>
import Burger from '../components/Burger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'

const socket = io();

/*function menuItem(name, pic, calories, lactose, gluten) {
  this.name = name; // The *this* keyword refers to the object itself
  this.url = pic;
  this.kCal= calories;
  this.lactose = lactose;
  this.gluten = gluten;
}
const menuArray=[new menuItem("Good Fella","img/simple_burger.jpg",100,true,false),
  new menuItem("Ugly Bastard","img/ugly_burger.jpg",200,false,false),
  new menuItem("Big Boy","img/big_burger.jpg",300,true,true)]
console.log(menuArray);*/


export default {
  name: 'Home',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: menu,
      location: { x: 0,
            y: 0
          },
      fn: '',
      em:'',

      gender:'',
      payment:'Swish',
      orderedBurgers:{"Good Fella":0,"Ugly Bastard":0,"Big Boy":0}
    }
  },
  methods: {

    setLocation:function(event){
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};
      this.location.x=event.clientX- 10 - offset.x;
      this.location.y=event.clientY- 10 - offset.y;
    },
    markDone:function(){
    console.log(this.fn,this.house,this.payment,this.gender,this.orderedBurgers);
    socket.emit("addOrder", { orderId: this.getOrderNumber(),
                              details: { x: this.location.x,
                                         y: this.location.y,
                                         name:this.fn,
                                         email:this.em,
                                         gender:this.gender,
                                         payment:this.payment
                                          },
                              orderItems: this.orderedBurgers
                            }
               );
    },

    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
    addToOrder: function (event) {
  this.orderedBurgers[event.name] = event.amount;
  },
    addOrder: function (event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};
      socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: { x: event.clientX - 10 - offset.x,
                                           y: event.clientY - 10 - offset.y },
                                orderItems: ["Beans", "Curry"]
                              }
                 );
      this.location.x=event.clientX- 10 - offset.x;
      this.location.y=event.clientY- 10 - offset.y;
      console.log(this.location.x, this.location.y);
    }
  }
}
</script>

<style>
  #map {
    width: 1920px;
    height: 1078px;
    background: url("/img/polacks.jpg");
    background-repeat: no-repeat;
    position: absolute;

  }
  #mapBox{
  position:relative;
  width: 500px;
  height:500px;
  overflow:scroll;
  }
  @import 'https://fonts.googleapis.com/css?family=Pacifico|Dosis';

  /*https://www.w3schools.com/colors/colors_names.asp se länk för olika färger*/
  body {
     font-family: Arial, sans-serif;
     font-size: 1em;
  }
  .ingrediens {
  font-weight:bold ;
  }

  #burgers{
    background-color:	#383838;
    color: white;
  }

  #orderButton:hover {
     background-color:green;
     cursor:pointer;
  }

  #orderButton{
    margin-left: 5em;
  }
  section{
    margin:2em;
    border: 2px dotted black;
    border-radius: 25px;
    padding-left: 2em;
  }


  footer{
    font-size:1em;
  }

  .wrapper {
       display: grid;
       grid-template-columns: 300px 300px 300px;
       justify-content: center;

       /*background-color: #fff;
       color: #444;*/
   }

   .box {
       /*background-color: #444;
       color: #fff;*/
       border-radius: 20px;
       padding: 20px;
   }
   .box img{
   height:100px;
   width:auto;
   }
   header{
     margin:2em;
     border: 2px dotted black;
     border-radius: 25px;
     height: 300px;
     overflow:hidden;
   }
   #logo{
     opacity:0.7;
     width: 100%;
     height: auto;
   }
   #headline{
     position: absolute;
     padding-left: 13em;
     margin-top: -12em;
     color:white;

   }
   #map div {
     position: absolute;
     background: black;
     color: white;
     border-radius: 10px;
     width:20px;
     height:20px;
     text-align: center;
   }

</style>
