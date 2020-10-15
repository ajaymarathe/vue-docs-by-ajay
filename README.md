# Vue Docs by Ajay
Small docs snippet to understand whole Vue.

```
<script type="text/javascript">
	
	// vue routing 
import Vue from 'vue'
import VueRouter from 'vue-router'
Vue.use(VueRouter)

import Home from './components/home.vue';
import Show from './components/show.vue';


const routes = [
    {
        path: '/',
        component: Home,
        name: 'Home',
    },
    {
        path: '/show/:slug',
        component: Show,
        name: 'Show',
        props: true
    }
]

const router = new VueRouter({
    routes,
})

export default router

// how to pass params in url 
 <router-link :to="{name: 'Show', params: {slug: post.slug}}"  class="btn btn-primary">Go to Foo</router-link> 
let slug = this.$route.params.slug; //get the slug or id
{
	path: '/show/:slug', //route should be like this
	component: Show,
	name: 'Show',
	props: true
}

// vuex, state management using vuex
import Vue from 'vue'
import Vuex from 'vuex'
import router from './router.js'

Vue.use(Vuex)

export default new Vuex.Store({
    state: {
        post: '',
        single_post:'',
        categories:'',
        category:'',
    },
    mutations: {
        get_Post(state,get_Post){
            state.post = get_Post
        },
        get_single_post(state, get_single_post){
            state.single_post = get_single_post
        },
        get_categories(state,get_categories){
            state.categories = get_categories
        },
        get_single_category(state,get_single_category){
            state.category = get_single_category
        }
    },
    getters: {
        posts_data: state => {
            return state.post;
        },
        single_post: state =>{
            return state.single_post;
        },
        categories: state =>{
            return state.categories
        },
        category: state => {
            return state.category
        }
    },
    actions: {
        // get all post
        getPost({ commit }){
            axios.get('http://localhost:8000/api/posts')
            .then(response => {
                commit('get_Post', response.data)
                console.log("this is response", response);
            })
            .catch(function (error) {
                // handle error
                console.log(error);
            })
        },
        // get the single post
        get_single_post({commit},slug){
            console.log('get single post');
            axios.get('http://localhost:8000/api/posts/'+slug)
            .then( res => {
                console.log(res);
                commit('get_single_post', res.data)
            })
            .catch((error)=>{
                console.log(error);
            });
        },
        // Edit the post
        Edit_Post(UpdateData,slug){
            axios.post('http://localhost:8000/api/posts/'+slug,{
                title: UpdateData[0],
                category: UpdateData[1],
                body: UpdateData[2]
            })
            .then(res=>{
                console.log(res);
            })
            .catch((error) => {
                console.log(error);
            })
        },
        // delete the post
        Delete_Post({commit},slug){
            axios.delete('http://localhost:8000/api/posts/'+slug)
            .then(res => {
                console.log("this is res",res);
            })
            .catch((error) => {
                router.push("/")
                console.log("this is error",error);
            })
        },
        // get categories
        get_Categories({commit}){
            axios.get('http://localhost:8000/api/category')
            .then(res =>{
                console.log(res.data)
                commit('get_categories',res.data)
            })
            .catch((error) => {
                console.log("this is error",error);
            })
        },
        // add the post
       add_Post({commit},PostData){
            axios.post("http://localhost:8000/api/posts",{
                title: PostData[0],
                category: PostData[1],
                body: PostData[2]
            })
            .then(res => {
                router.push("/")
                console.log("this is response",res)
            }) 
            .catch((error) => {
                 
                console.log("this is error",error);
            })  
        },
        // delete the category
        Delete_Category({dispatch,commit},category_id){
            console.log('delete id',category_id)
            axios.delete('http://localhost:8000/api/category/'+category_id)
            .then(res => {
                dispatch('get_Categories')
                console.log("this is res",res);
            })
            .catch((error) =>{
                console.log(error);
            });
        },
        get_single_category({commit},id){
            axios.get('http://localhost:8000/api/category/'+id)
            .then(res =>{
                console.log(res.data)
                commit('get_single_category',res.data)
            })
            .catch((error) =>{
                console.log("this is error",error)
            })
        },
        Create_Category({commit},CategoryData){
            axios.post('http://localhost:8000/api/category/',{
                category: CategoryData[0],
                user_id: 2
            })
            .then(res =>{
                console.log(res)
                router.push('category')
            })
            .catch((error) => {
                console.log(error)
            })
        },
        Update_Category({commit},CatData){
            axios.patch('http://localhost:8000/api/category/'+CatData[1],{
                category: CatData[0],
                user_id: 2,
            })
            .then(res => {
                console.log(res)
            })
            .catch((error) => {
                console.log(error)
            })
        }
    }
  })
  
  
  ```
```
//create forms
<template>
	<div>
		<div class="container">
			<div class="row">
				<div class="col-md-8">
					<h1>Create post</h1>
					<div class="form-group">
						<label>Title:</label>
						<input v-model="title" class="form-control" type="text" placeholder="Enter the Title">
					</div>
					<div class="form-group">
						<label>Category:</label>
						<select v-model="category" class="custom-select" >
							<option v-for="category in categories" :value="category.id">{{category.category}}</option>
						</select>
					</div>
					<div class="form-group">
						<ckeditor :editor="editor" v-model="body" :config="editorConfig"></ckeditor>
					</div>
					<div class="form-group">
						<input @click="CreatePost" type="submit" class="btn btn-primary">
					</div>
				</div>
			</div>
		</div>
	</div>
</template>
<script type="text/javascript">
import {  mapGetters } from 'vuex'	
import ClassicEditor from '@ckeditor/ckeditor5-build-classic';
	export default {
		data(){
			return {
				title:'',
				category:'',
				editor: ClassicEditor,
                body: '<p>Content of the editor.</p>',
                editorConfig: {
                    // The configuration of the editor.
                }
			}
		},
		mounted(){
			this.$store.dispatch('get_Categories');
		},
		computed:{
		...mapGetters([
            'categories'
            ])
		},
		methods:{
			CreatePost(){
				let PostData = [this.title, this.category, this.body];
				console.log(PostData);

				this.$store.dispatch('add_Post',PostData);
			}
		}
	}
</script>

// edit and update 

<template>
	<div>
		<div class="container">
			<div class="row">
				<div class="col-md-8">
					<h1>Category Edit</h1>
					{{category}}
					<hr>
					<div class="form-group">
						<label>Category:</label>
						<input :value="category.category" type="text" class="form-control">
					</div>
					<div class="form-group">
						<button @click="UpdateCategory" type="button" class="btn btn-primary">Submit</button>
					</div>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
import {  mapGetters } from 'vuex'
export default {
  name: 'categoryEdit',
  data () {
    return {

    }
  },
  mounted(){
  	const id = this.$route.params.id;
  	console.log(id);

  	this.$store.dispatch('get_single_category',id);
  },
  computed:{
	  ...mapGetters([
	    'category'
	    ])
  },
  methods:{
  		UpdateCategory(){
  			const id = this.$route.params.id;
  			const CatData = [this.category.category,id];
  			console.log(CatData)
  			this.$store.dispatch('Update_Category',CatData);
  		}
  }
}
</script>

<style lang="css" scoped>
</style>

//redirect and back in vue

router.push('category') // router.push to go specific route.
router.back() // back to previous page.

// pass headers through the axios call.
    axios.post('http://localhost:8000/api/auth/me',{
       headers: {
	Authorization: 'Bearer ' + localStorage.getItem("access_token") 
    }})
    //....

</script>
```

## Implement realtime validation with Vee-Validate

```
<template>
  <div>
    <div class="container">
      <h1>Vee Validate Form</h1>
    <ValidationObserver v-slot="{ handleSubmit }">
      <form @submit.prevent="handleSubmit(onSubmit)">
        <ValidationProvider
          name="Name"
          rules="required|alpha"
          v-slot="{ errors }"
        >
          <div class="form-group">
            <label>Name</label>
            <input class="form-control" v-model="formData.name" />
            <span class="error">{{ errors[0] }}</span>
          </div>
        </ValidationProvider>

        <ValidationProvider
          name="E-mail"
          rules="required|email"
          v-slot="{ errors }"
        >
          <div class="form-group">
            <label>Email</label>
            <input class="form-control" v-model="formData.email" type="email" />
            <span class="error">{{ errors[0] }}</span>
          </div>
        </ValidationProvider>

        <ValidationProvider
          name="Password"
          rules="required|min:6|max:12"
          v-slot="{ errors }"
        >
          <div class="form-group">
            <label>Password</label>
            <input
              class="form-control"
              v-model="formData.password"
              type="password"
            />
            <span class="error">{{ errors[0] }}</span>
          </div>
        </ValidationProvider>

        <ValidationProvider name="Gender" rules="required" v-slot="{ errors }">
          <div class="form-group">
            <label>Gender</label>
            <select class="form-control" v-model="formData.gender">
              <option value="male">Male</option>
              <option value="female">Female</option>
              <option value="other">Other</option>
            </select>
            <span class="error">{{ errors[0] }}</span>
          </div>
        </ValidationProvider>
        <ValidationProvider
          name="AcceptTerms"
          rules="required"
          v-slot="{ errors }"
        >
          <div class="form-check">
            <input
              class="form-check-input"
              type="checkbox"
              v-model="formData.acceptTerms"
            />
            <label class="form-check-label">Accept Terms </label>
            <span class="error">{{ errors[0] }}</span>
          </div>
        </ValidationProvider>
        <input class="btn btn-primary mt-2" type="submit" text="submit" />
      </form>
    </ValidationObserver>
    </div>
  </div>
</template>

<script>
export default {
  name: "VeeValidateForm",
  data: () => ({
    formData: {
      name: "",
      email: "",
      password: "",
      gender: "",
      acceptTerms: null,
    },
  }),
  methods: {
    onSubmit() {
      console.log(this.formData);
    },
  },
};
</script>

<style scoped>
.error {
  color: red;
}
</style>

```
