<template>
    <div class="container">
      <div class="wizard"
      v-bind:class="{wizardAnimation: wizardAnimation}">
        <div class="wizard-inner">
          <div class="connecting-line"></div>
          <ul class="nav nav-tabs" role="tablist">
            <li v-for="tab in tabs" :key="tab.id"
              v-bind:class="{ active: step == tab.id }" role="presentation">
              <a href="#step1" data-toggle="tab" aria-controls="step1" role="tab" title="Step 1">
                  <span class="round-tab">
                    <i :class="tab.icon"></i>
                  </span>
                </a>
              </li>
            </ul>
        </div>
      <form role="form">
        <div class="container">
          <div class="col-md-12">
            <div class="tab-content">
              <div v-if="step == 1" class="tab-pane active" role="tabpanel">
                  <h3>Step 1: Log In to your Trello account</h3>
              </div>
              <div v-if="step == 2" class="tab-pane active" role="tabpanel">
                <div v-if="loading == true">
                  <div class="center loader"></div>
                </div>
                <div v-else>
                <h3>Step 2: Select a board</h3>
                  <div class="row">
                    <div class="col-md-3" 
                      v-for="board in boards" 
                      :key="board.id"
                      @click="selectItem(board.id), selectBoard(board), nextstep()"
                    >
                      <div class="panel panel-default" :class="{highlight:board.id == selected}">
                        <div class="panel-body board" v-bind:style="{ 'background-image': 'url(' + board.prefs.backgroundImage + ')' }">
                          <h5>{{board.name}}</h5>
                          Last Activity
                          {{board.dateLastActivity | lastDate}}
                        </div>
                      </div>
                    </div>
                  </div>                        
                </div>
                </div>
                <div v-if="step == 3" class="tab-pane active" role="tabpanel">
                  <div v-if="loading == true">
                  <div class="center loader"></div>
                </div>
                <div v-else>
                  <h3>Step 3: Select your user story to edit</h3>
                  <h4>Your current score is: </h4>                 
                  <div class="row">
                    <card-modal v-if="cardModal" @close="cardModal = false" :currentCard="currentCard" v-on:update="refreshData()">
                    </card-modal>
                    <div class="col-md-4" v-for="card in cards" 
                      :key="card.id" 
                    >
                      <div class="panel panel-default"
                        @click="cardModal = true,  currentCard = card"
                      >
                        <div class="panel-heading card">
                          {{card.name}}
                          
                          <!-- <textarea type="text" class="cardtext form-control" placeholder="Userstory" aria-describedby="basic-addon1" v-model=card.name></textarea> -->
                        </div>
                        <!-- <div class="panel-body">
                            
                        </div> -->
                      </div>
                    </div>
                    <!-- <div class="col-md-3">
                      <div class="panel panel-default ">
                        <div class="panel-heading board">
                          <textarea type="text" class="cardtext form-control" placeholder="Userstory" aria-describedby="basic-addon1" v-model="newCardName"></textarea>
                          <h5>Select List</h5>
                          <select v-model="selectedList" name="selectedList">
                            <option v-for="list in this.boardLists" :key="list.id" :value="list.id">{{list.name}}</option>
                          </select>
                        </div>
                        <div class="panel-body">
                          <button type="button" class="btn btn-info" @click="addCard()">Add</button>
                        </div>
                      </div>
                    </div> -->
                  </div>             
                </div>
                </div>
                <div v-if="step == 4" class="tab-pane active" role="tabpanel">
                  <div v-if="loading == true">
                  <div class="center loader"></div>
                </div>
                <div v-else>
                  <h3>Results</h3>
                  <div class="row">
<div class="col-md-12">
    <div class="result container">
        <div class="row">
            <div class="col-md-6"> 
               <div id="specificChart" class="donut-size">
                  <div class="pie-wrapper">
                    <span class="label">
                      <span class="num">{{averageUserstoriesScore}}</span><span class="smaller">%</span>
                    </span>
                    <div v-if="averageUserstoriesScore < 50" class="pie" style="clip: rect(auto, auto, auto, auto);">
                      <div :style="{ transform: 'rotate('+(360 * (averageUserstoriesScore / 100)) +'deg)'}" class="left-side half-circle" style="border-width: 0.1em;transform: rotate(180deg);"></div>
                      <div :style="{ transform: 'rotate('+ 180 +'deg)'}" class="right-side half-circle" style="border-width: 0.1em;"></div>
                    </div>
                    <div v-else class="pie" style="clip: rect(0, 1em, 1em, 0.5em);">
                      <div :style="{ transform: 'rotate('+(360 * (averageUserstoriesScore/ 100)) +'deg)'}" class="left-side half-circle" style="border-width: 0.1em;transform: rotate(180deg);"></div>
                      <div :style="{ transform: 'rotate('+ 0 +'deg)'}" class="right-side half-circle" style="border-width: 0.1em;"></div>
                    </div>
        
                    <div class="shadow" style="border-width: 0.1em;"></div>
                    </div>                    
                </div>
                <center><span>Users Average</span></center>
              </div>


      <div class="col-md-6"> 
               <div id="specificChart" class="donut-size">
                  <div class="pie-wrapper">
                    <span class="label">
                      <span class="num">{{ownAverageUserstoriesScore}}</span><span class="smaller">%</span>
                    </span>
                    <div v-if="ownAverageUserstoriesScore < 50" class="pie" style="clip: rect(auto, auto, auto, auto);">
                      <div :style="{ transform: 'rotate('+(360 * (ownAverageUserstoriesScore / 100)) +'deg)'}" class="left-side half-circle" style="border-width: 0.1em;transform: rotate(180deg);"></div>
                      <div :style="{ transform: 'rotate('+ 180 +'deg)'}" class="right-side half-circle" style="border-width: 0.1em;"></div>
                    </div>
                    <div v-else class="pie" style="clip: rect(0, 1em, 1em, 0.5em);">
                      <div :style="{ transform: 'rotate('+(360 * (ownAverageUserstoriesScore/ 100)) +'deg)'}" class="left-side half-circle" style="border-width: 0.1em;transform: rotate(180deg);"></div>
                      <div :style="{ transform: 'rotate('+ 0 +'deg)'}" class="right-side half-circle" style="border-width: 0.1em;"></div>
                    </div>
        
                    <div class="shadow" style="border-width: 0.1em;"></div>
                    </div>                    
                </div>
                <center><span>Your Average</span></center>
              </div></div></div>
        </div>



                  <div class="col-md-12" v-for="card in lambdaCards" 
                      :key="card.id" 
                    >
                      <div class="result container">
                        <div class="row">
            <div class="col-md-6">
                        <div class="panel-heading board">
                          <textarea readonly type="text" class="cardtext form-control" placeholder="Userstory" aria-describedby="basic-addon1" v-model=card.name></textarea>
                        </div></div><div class="col-md-6"> 
                        <div id="specificChart" class="donut-size">
      <div class="pie-wrapper">
        <span class="label">
          <span class="num">{{card.suggestions.score}}</span><span class="smaller">%</span>
        </span>
        <div v-if="card.suggestions.score < 50" class="pie" style="clip: rect(auto, auto, auto, auto);">
          <div :style="{ transform: 'rotate('+(360 * (card.suggestions.score / 100)) +'deg)'}" class="left-side half-circle" style="border-width: 0.1em;transform: rotate(180deg);"></div>
          <div :style="{ transform: 'rotate('+ 180 +'deg)'}" class="right-side half-circle" style="border-width: 0.1em;"></div>
        </div>
        <div v-else class="pie" style="clip: rect(0, 1em, 1em, 0.5em);">
          <div :style="{ transform: 'rotate('+(360 * (card.suggestions.score / 100)) +'deg)'}" class="left-side half-circle" style="border-width: 0.1em;transform: rotate(180deg);"></div>
          <div :style="{ transform: 'rotate('+ 0 +'deg)'}" class="right-side half-circle" style="border-width: 0.1em;"></div>
        </div>
        
        <div class="shadow" style="border-width: 0.1em;"></div>
      </div>
    </div></div>
    <div class="col-md-12"><h4>Tags</h4><span class="badge badge-info" v-for="tag in card.tags" :key="tag.id">{{tag.replace('{"').replace('"}','').replace('.','')}}</span></div><div class="col-md-12"><h4>Penalties</h4>
    <div v-for="suggestion in card.suggestions.suggestions" :key="suggestion.id" class="alert alert-danger" role="alert">
  <b>- {{suggestion.penaltypoints}}</b> {{suggestion.message}}
</div></div>
                      </div>
        </div>
                    </div>
                </div>
                </div>
                </div>
                <div v-if="step == 1" class="float-right">
                  <button @click="authenticate()" id="loginButton" type="button" class="btn btn-primary">Log in</button>
                </div>
                <div class="float-right">
                 <button type="button" class="btn btn-default prev-step" v-if="step > 2" v-on:click="step -= 1" >Previous</button>
                 <button type="button" class="btn btn-primary next-step" v-if="step < 4 && step > 2" v-on:click="nextstep()" >Next</button>
                </div>
              </div>
            </div>
          </div>
        </form>
      </div>
    </div>    
</template>
<script>
import axios from "axios";
import cardModal from "./modals/cardmodal.vue";
export default {
  name: "Wizard",
  components: {
    "card-modal" : cardModal
  },
  data() {
    return {
      currentCard: "",
      cardModal: false,
      wizardAnimation: false,
      averageUserstoriesScore: 0,
      ownAverageUserstoriesScore: 0,
      loading: false,
      selected: "",
      newCardName: "",
      selectedList: "",
      selectedBoard: {},
      boardLists: [],
      lambdaCards: [],
      cards: [],
      boards: [],
      step: 1,
      tabs: [
        { id: 1, icon: "fab fa-trello" },
        { id: 2, icon: "fas fa-th-list" },
        { id: 3, icon: "fas fa-user-check" },
        { id: 4, icon: "fas fa-cloud-upload-alt" }
      ]
    };
  },
  watch:{
  },
  filters: {
    lastDate(date) {
      if (date != null) {
        date = date.substring(0, 9);
        return date;
      }
    }
  },
  methods: {
    testlog(){
      console.log('test');
    },
    selectItem(e) {
      this.selected = e;
    },
    selectBoard(e) {
      this.selectedBoard = e;
      this.loading = true;
    },
    addCard() {
      Trello.post(
        "/cards",
        {
          idList: this.selectedList,
          name: this.newCardName
        },
        result => {
          //success
          this.$swal({
            title: "Card successfully added to Trello",
            timer: 1000,
            type: "success",
            showConfirmButton: false,
            });
          this.refreshData();
        },
        result => {
          //error
          alert("Add failed");
          console.log(result);
        }
      );
    },
    getAllCardsOfSingleBoard() {
      this.cards = "";
      var id = this.selected;
      Trello.boards.get(
        id + "/cards",
        result => {
          //success
          this.cards = result;
        },
        result => {
          //error
          alert(result);
        }
      );
    },
    getListsFromBoards() {
      var id = this.selected;
      Trello.boards.get(
        id + "/lists/",
        result => {
          //success
          this.boardLists = result;
          this.loading = false;
        },
        result => {
          //error
          alert(result);
        }
      );
    },
    getAllBoards() {
      Trello.members.get(
        "me/boards",
        result => {
          //success
          this.boards = result;
          this.loading = false;
        },
        result => {
          //error
          alert(result);
        }
      );
    },
    authenticate() {
      let $self = this;
      window.Trello.authorize({
        type: "popup",
        name: "Userstory Analyzer",
        scope: {
          read: "true",
          write: "true"
        },
        expiration: "never",
        success: this.authenticationSuccess,
        error: this.authenticationFailure
      });
    },
    authenticationSuccess() {
      // this.$swal({
      //   title: "Authentication Succeedded",
      //   timer: 1000,
      //   type: "success",
      //   showConfirmButton: false,
      //   });
      this.getAllBoards();
      this.step++;
      this.loading = true;
      this.wizardAnimation = true;
    },
    authenticationFailure() {
      this.$swal("Authentication Failed");
    },
    nextstep() {
      this.step++;
      if (this.step == 3) {
        this.refreshData();
      }
      if (this.step == 4) {
        this.getAverageScoreLambda();
        this.lambdaCards = [];
        this.getLambdas(this.cards);
        this.$store.commit("addBoard", {
          id: this.selectedBoard.id,
          name: this.selectedBoard.name,
          cards: this.lambdaCards
        });
      }
    },
    previousstep() {
      this.step--;
    },
    refreshData() {
      this.getAllCardsOfSingleBoard();
      this.getListsFromBoards();
    },
    async getAverageScoreLambda() {
      var averageUserstoriesScore;

      await axios
        .get(
          "https://cd5zq44552.execute-api.eu-central-1.amazonaws.com/dev/myTrelloService/getAllcardsAverage"
        )
        .then(function(response) {
          averageUserstoriesScore = response.data.data;
        })
        .catch(error => error);

      this.averageUserstoriesScore = averageUserstoriesScore;
    },
    async getLambdas(cards) {
      var ownAverageUserstoriesScore = 0;      
      var tags;
      var suggestions;
      var test = 0;
      var totalBoardScore = 0;
      var formattedcards = this.cards.map(card => {
        return { story: card.name };
      });

      $.ajax({
        type: "POST",
        url:
          "https://cd5zq44552.execute-api.eu-central-1.amazonaws.com/dev/myTrelloService/getStoryCategories",
        data: JSON.stringify(formattedcards),
        async: false,
        success: function(response) {
          tags = response.data;
          console.log(tags);
        }
      });

    // Add stories to database
      $.ajax({
        type: "POST",
        url:
          "https://cd5zq44552.execute-api.eu-central-1.amazonaws.com/dev/myTrelloService/saveStories",
        data: JSON.stringify(formattedcards),
        async: false,
        success: function(response) {
        }
      });

      $.ajax({
        type: "POST",
        url:
          "https://cd5zq44552.execute-api.eu-central-1.amazonaws.com/dev/myTrelloService/getScore",
        data: JSON.stringify(formattedcards),
        async: false,
        success: function(response) {
          suggestions = response.data;

          ownAverageUserstoriesScore = response.averagescore;

          // console.log(ownAverageUserstoriesScore);
          console.log(suggestions);
        }
      });

      this.ownAverageUserstoriesScore = ownAverageUserstoriesScore;

      console.log('USER SCORE: ' + this.ownAverageUserstoriesScore);
      
      for (var i = 0; 1 < cards.length; i++) {
        let card = cards[i];
        this.lambdaCards.push({
          id: card.id,
          name: card.name,
          suggestions: suggestions[i],
          tags: tags[i]
        });
      }
      
      console.log(this.lambdaCards);
      this.loading = false;
    }
  }
};
</script>