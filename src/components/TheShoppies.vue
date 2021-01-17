<template>

    <div id="pagecontainer">
        <h1 style="margin-top:30px; margin-bottom:20px">The Shoppies</h1>

        <div id="nomination" class="border shadow-sm">
            <b-alert variant="success"  :show="nominated.length >= 5" dismissible>5 Nominees are successfully selected!</b-alert>
                <h5 class="titletext">Nominated |<b-button variant="link" v-on:click="removeAll()">Clear All</b-button>
                </h5>
                <div class="row">
                    <div class="imgContainer col-sm-2" v-for="movie in nominated" :key="movie">
                        <img :src=movie.Poster alt="Avatar" class="image" style="width:100%">
                        <div class="middle">
                            <div class="text">{{movie.Title}}</div>
                            <div class="text">{{movie.Year}}</div>
                            <button type="button" class="btn btn-secondary" v-on:click="remove(movie)">Remove</button>
                        </div>
                    </div>
                </div>
        </div>

        <div id="moviebox" class="border shadow-sm">

            <div class="form-group col-sm-6 ">
                <h5>Movie Title</h5>
                <div class="inner-addon left-addon">
                    <b-icon-search class="left-icon"></b-icon-search>
                    <input  type="text" v-model="searchList" class="form-control main" placeholder="Search" />
                </div>
            </div>
        </div>

        <div id="bottom">
            <div id="result" >
                <h5 class="titletext">Result for {{searchList}}</h5>
                <div class="row">
                <div class="imgContainer col-sm-2" v-for="movie in movieList" :key="movie">
                    <img :src=movie.Poster alt="Avatar" class="image" style="width:100%" v-bind:class="{ imageHold: isNominated(movie) }">
                    <div class="middle" v-bind:class="{ middleHold: isNominated(movie) } ">
                        <div class="text">{{movie.Title}}</div>
                        <div class="text">{{movie.Year}}</div>
                        <button type="button" class="btn btn-secondary" v-on:click="nominate(movie)" :disabled="isNominated(movie)">Nominate<span v-if="nominated.includes(movie)">d</span></button>
                    </div>
                </div>
                </div>
                <b-pagination
                        v-model="currentPage"
                        :total-rows="rows"
                        :per-page="perPage"
                        first-text="First"
                        prev-text="Prev"
                        next-text="Next"
                        last-text="Last"
                        align="center"
                ></b-pagination>
            </div>

        </div>
    </div>
</template>

<script>
    import axios from 'axios';
    export default {
        name: "TheShoppies",
        data(){
            return{
                movieList: [],
                nominated: [],
                searchList: "",
                currentPage:1,
                rows: 10,
                perPage:10,
            };
        },

        watch: {
          searchList() {
              this.currentPage = 1;
              this.getMovies();
          },
            currentPage(){
                this.getMovies();
            }

        },

        methods: {
            getMovies() {
                console.log("getting movies...");
               let target = "http://www.omdbapi.com/?apikey=dce8b23b&type=movie&s=" + this.searchList + "&page=" + this.currentPage;
                axios.get(target)
                    .then((response) => {
                        if (response.status == 200) {
                            console.log("done!");
                            this.movieList = response.data.Search;
                            this.rows=response.data.totalResults;
                        }
                    });
            },
            nominate(movie){
                this.nominated.push (movie);


            },

            isNominated(movie){
                if (this.nominated.includes(movie) | this.nominated.length >= 5)
                {
                    return true;
                }

            },

            remove(movie){
                const index = this.nominated.indexOf(movie);
                if (index > -1) {
                    this.nominated.splice(index, 1);
                }
            },

            removeAll(){
                this.nominated = []
            },


        }


    }
</script>

<style scoped>
    .inner-addon {
        position: relative;
    }

    .inner-addon .left-icon {
        position: absolute;
        padding: 10px;
        pointer-events: none;
        height: 38px;
        width: 38px;
    }

    .left-addon .left-icon { left: 0px; }
    .left-addon input { padding-left: 30px; }

    #pagecontainer{
        width: 70%;
        margin: auto
    }
    .titletext{
        padding-left: 50px;
    }

    #moviebox {
        padding-top:20px;
        padding-left: 50px;
    }

    #result {
        border:darkgrey solid 1px;
        padding:10px;


    }

    #nomination {
        border:darkgrey solid 1px;
        margin-bottom: 39px;
        padding:10px;

    }

    #bottom {
        padding-top: 20px;
        padding-bottom: 20px;
        margin-top: 10px;
        overflow: hidden;
    }

    .imgContainer {
        position: relative;
        width: 15%;
        margin: 15px;
        float: left;
    }

    .image {
        opacity: 1;
        display: block;
        width: 100%;
        height: auto;
        transition: .5s ease;
        backface-visibility: hidden;
    }

    .middle {
        transition: .5s ease;
        opacity: 0;
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        -ms-transform: translate(-50%, -50%);
        text-align: center;
    }

    .imgContainer:hover .image {
             opacity: 0.3;
         }

    .imgContainer:hover .middle {
        opacity: 1;
    }

    .imageHold {
        opacity: 0.3;

    }

    .middleHold {
        opacity: 1;
    }

    .row{
        justify-content:center;
    }


</style>
