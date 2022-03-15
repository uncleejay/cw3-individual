<template>
  <div id="app">
    <div class="jumbotron">
			<div class="container">
				<div class="row">
					<div class="col-md-2">
						<h2 class="logo">Lesson</h2>
					</div>
					<div class="col-md-6">
						<form>
							<input type="text" class="form-control" v-model="term" v-on:keyup="searchLessons" placeholder="Search">
						</form>
					</div>
					<div class="col-md-4">
						<!-- Hides and shows the cart depending on whether or not it is empty -->
						<button class="cart-btn btn btn-primary" v-if="cartItemCount === ''" hidden><i class="fa fa-shopping-cart"></i> Cart {{cartItemCount}}</button>
						<button class="cart-btn btn btn-primary" v-on:click="showShoppingCartPage()" v-else><i class="fa fa-shopping-cart"></i> Cart {{cartItemCount}}</button>
						<!-- End of Hides and shows the cart depending on whether or not it is empty -->
					</div>
				</div>
			</div>
		</div>
		<nav aria-label="breadcrumb">
		  <ol class="breadcrumb">
		    <li class="breadcrumb-item"><a href="index.html">Home</a></li>
		    <li class="breadcrumb-item active" aria-current="page">{{homepage}}</li>
		  </ol>
		</nav>
  </div>
</template>

<script>
import Lessons from './components/Lessons.vue'
import Checkout from './components/Checkout.vue'

export default {
  name: 'App',
  components: {
    Lessons,
    Checkout
  },
  data() {
    return {

       lessons: [],
      
        cartItems: [

        ],
        showProduct: true,
        searchTxt: '',
        
        
    }
  },
  methods: {
    addLessonToCart: function (lessonID, lesson) {
          
            const lessonIndex = this.lessons.findIndex(lesson => lesson._id === lessonID);
            if (lessonIndex != -1) {
                console.log("heeeree");
                this.lessons[lessonIndex].space -= 1;       
            }
             this.addToCart(lessonID, lesson);
        },
        
        updateShowProduct: function () {
            this.showProduct = !this.showProduct;
        },
        addToCart: function (lessonID, lesson) {
          console.log("here");
            const itemIndex = this.cartItems.findIndex(item => item.lessonID === lessonID);
            if (itemIndex != -1) {
                this.cartItems[itemIndex].space += 1;
            } else {
                this.cartItems.push({ lessonID: lessonID, space: 1, lesson: lesson });
            }
        },
        removeLessonFromCart: function (index) {
            this.cartItems[index].space = this.cartItems[index].space - 1;
            const lessonIndex = this.lessons.findIndex(lesson => lesson._id === this.cartItems[index].lessonID);
            if (lessonIndex != -1) {
                this.lessons[lessonIndex].space += 1;
            }
            if (this.cartItems[index].space == 0) {
                this.cartItems.splice(index, 1);
            }

            if(this.cartItems.length == 0){
              this.showProduct = true;
            }

            console.log(this.cartItems);


        },
        cartCount: function(){
            let cartQuantity = 0;
            for (let index = 0; index < this.cartItems.length; index++) {
                cartQuantity += this.cartItems[index].space;
                
            }
            return cartQuantity;
        },
        searchLessons: function () {
            
            fetch("https://cst3145-cw2-backend.herokuapp.com/collection/lessons/" + this.searchTxt).then(
            (response) => {
                response.json().then(
                    (data) => {
                        console.log(data);
                        this.lessons = data;
                        console.log(this.lessons);
                    });
            })
        },
        checkOut: function (checkOutName, checkOutNumber) {
            fetch('https://cst3145-cw2-backend.herokuapp.com/collection/orders', {
                method: 'POST', // set the HTTP method as 'POST'
                headers: {
                    'Content-Type': 'application/json', // set the data type as JSON
                },
                body: JSON.stringify({ name: checkOutName, phone: checkOutNumber, lessons: this.cartItems }), // need to stringify the JSON object
            })
                .then(response => response.json())
                .then(responseJSON => {
                    console.log('Success:', responseJSON);
                });

            for (let index = 0; index < this.cartItems.length; index++) {
                const lesson = this.cartItems[index].lesson;
                fetch(`https://cst3145-cw2-backend.herokuapp.com/collection/lessons/${lesson._id}`, {
                    method: 'PUT', // set the HTTP method as 'POST'
                    headers: {
                        'Content-Type': 'application/json', // set the data type as JSON
                    },
                    body: JSON.stringify({ space: lesson.space}), // need to stringify the JSON object
                })
                    .then(response => response.json())
                    .then(responseJSON => {
                        console.log('Success:', responseJSON);
                    });
            }
            // alert("Check out successful\nYour order has been submitted");
            this.showProduct = true;
            this.cartItems = [];
            this.$swal({
                title: "Check out successful",
                text: "Your order has been submitted",
                icon: "success",
            });
            
        },
        fetchLesson: function (_id) {
            const lessonIndex = this.lessons.findIndex(lesson => lesson._id === _id);
            if (lessonIndex == -1) {
                return;
            }
            return this.lessons[lessonIndex];
        }
       
    },
    
    created: function () {
        fetch("https://cst3145-cw2-backend.herokuapp.com/collection/lessons").then(
             (response) => {
                response.json().then(
                     (data) => {
                        console.log(data);
                        this.lessons = data;
                        console.log(this.lessons + "Fetched");
                    });
            })
    }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
