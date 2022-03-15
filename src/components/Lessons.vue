<template>
  <div class="mt-5">
    <div v-if="showProduct">
				<h2 class="text-center mt-5 mb-5"></h2>
				<div class="container">
					<div class="row">
						<div class="col-md-2">
						<aside>
							<h6>Sort By</h6>
							<div class="custom-control custom-radio">
							  <input type="radio" id="subject" v-model="sort.sortingMethod" value="subject" name="" class="custom-control-input">
							  <label class="custom-control-label" for="subject">Subject</label>
							</div>
							<div class="custom-control custom-radio">
							  <input type="radio" id="location" v-model="sort.sortingMethod" value="location" name="" class="custom-control-input">
							  <label class="custom-control-label" for="location">Location</label>
							</div>
							<div class="custom-control custom-radio mb-1">
							  <input type="radio" id="price" v-model="sort.sortingMethod" value="price" name="" class="custom-control-input">
							  <label class="custom-control-label" for="price">Price</label>
							</div>
							<div class="custom-control custom-radio mb-1">
							  <input type="radio" id="availability" v-model="sort.sortingMethod" value="availability" name="" class="custom-control-input">
							  <label class="custom-control-label" for="availability">Availability</label>
							</div>

							<!-- order by ascending or descedning order -->
							<h6 class="mt-4">Order By</h6>
							<div class="custom-control custom-radio mb-1">
							  <input type="radio" id="ascending" v-model="sort.sortingOrder" value="ascending" name="" class="custom-control-input">
							  <label class="custom-control-label" for="ascending">Ascending</label>
							</div>
							<div class="custom-control custom-radio mb-1">
							  <input type="radio" id="descending" v-model="sort.sortingOrder" value="descending" name="" class="custom-control-input">
							  <label class="custom-control-label" for="descending">Descending</label>
							</div>
							<!-- end of order by ascending or descending order -->

						</aside>
						</div>
						<!-- UI to display lesson items -->
						<div class="col-md-10 row">
              <div v-if="(lessons.length > 0)" class="">
							<div v-for="(lesson) in sortedLessons" :key="lesson.id" class="col-md-4">
							<div class="card mb-4">
								<img class="card-img" v-bind:src="lesson.image" alt="Lesson Image">
								<div class="card-body">
									<h6>Subject: {{lesson.subject}}</h6>
									<h6>Location: {{lesson.location}}</h6>
									<h6>Available: {{lesson.availablespace - cartCount(lesson)}}</h6>
									<h6>Price: £{{lesson.price}}</h6>
									<h6>Rating: 
										<!-- <span v-for="n in lesson.rating">★</span>
                    					<span v-for="n in 5 - lesson.rating">☆</span> -->
									</h6>
									<button v-on:click="addToCart(lesson)" v-if="canAddToCart(lesson)" class="btn btn-primary">Add to Cart</button>
									<!-- disable cart -->
									<button class="btn btn-primary" v-bind:disabled="(lesson.space <= 0)" v-on:click="addLesson(lesson._id, lesson)">Add to Cart</button>
									<!-- End of disable cart -->
								</div>
							</div>
						</div>
              </div>
						</div>
						<!-- end of UI to display lesson items -->
					</div>
				</div>
			</div>
  </div>
</template>

<script>
export default {
    name: 'Lessons',
    props: ['lessons'],
    methods: {
        addLesson: function (lessonID, lesson) {
             this.$emit('addLesson', lessonID, lesson);
        },
    },
    data() {
        return {
           
        order: 'asc',
        attribute: 'subject',
        sortOrders: {
            Ascending: 'asc',
            Descending: 'desc'
        },
        sortAttributes: {
            Subject: 'subject',
            Location: 'location',
            Price: 'price',
            Spaces: 'spaces'
        },
        }
    },
    computed: {
        sortedLessons() {
            function compareSubjectAsc(a, b) {
                if (a.subject > b.subject) return 1;
                if (a.subject < b.subject) return -1;
                return 0;
            }
            function compareSubjectDesc(a, b) {
                if (a.subject > b.subject) return -1;
                if (a.subject < b.subject) return 1;
                return 0;
            }

            function compareLocationAsc(a, b) {
                if (a.location > b.location) return 1;
                if (a.location < b.location) return -1;
                return 0;
            }
            function compareLocationDesc(a, b) {
                if (a.location > b.location) return -1;
                if (a.location < b.location) return 1;
                return 0;
            }

            function comparePriceAsc(a, b) {
                if (a.price > b.price) return 1;
                if (a.price < b.price) return -1;
                return 0;
            }
            function comparePriceDesc(a, b) {
                if (a.price > b.price) return -1;
                if (a.price < b.price) return 1;
                return 0;
            }

            function compareSpacesAsc(a, b) {
                if (a.space > b.space) return 1;
                if (a.space < b.space) return -1;
                return 0;
            }
            function compareSpacesDesc(a, b) {
                if (a.space > b.space) return -1;
                if (a.space < b.space) return 1;
                return 0;
            }

            if (this.order == "asc") {
                if (this.attribute == "subject") {
                  // eslint-disable-next-line vue/no-side-effects-in-computed-properties
                    return this.lessons.sort(compareSubjectAsc);
                } else if (this.attribute == "location") {
                  // eslint-disable-next-line vue/no-side-effects-in-computed-properties
                    return this.lessons.sort(compareLocationAsc);
                } else if (this.attribute == "price") {
                  // eslint-disable-next-line vue/no-side-effects-in-computed-properties
                    return this.lessons.sort(comparePriceAsc);
                } else {
                  // eslint-disable-next-line vue/no-side-effects-in-computed-properties
                    return this.lessons.sort(compareSpacesAsc);
                }

            } else {
                if (this.attribute == "subject") {
                  // eslint-disable-next-line vue/no-side-effects-in-computed-properties
                    return this.lessons.sort(compareSubjectDesc);
                } else if (this.attribute == "location") {
                  // eslint-disable-next-line vue/no-side-effects-in-computed-properties
                    return this.lessons.sort(compareLocationDesc);
                } else if (this.attribute == "price") {
                  // eslint-disable-next-line vue/no-side-effects-in-computed-properties
                    return this.lessons.sort(comparePriceDesc);
                } else {
                  // eslint-disable-next-line vue/no-side-effects-in-computed-properties
                    return this.lessons.sort(compareSpacesDesc);
                }
            }
        }
    },
}
</script>
