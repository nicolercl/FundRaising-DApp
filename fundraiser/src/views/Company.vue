<template>
  <div class="company">
    <v-container>
      <v-row>
        <v-col
          cols="4"
        >
          <div align="center">
            <v-img src="https://i.imgur.com/UVOo14B.png" aspect-ratio="1" max-width="150"></v-img>
            <br>
            <v-row>
              <v-col></v-col>
              <v-col>
                <v-img src="https://i.imgur.com/zQOYAeS.png" aspect-ratio="1" max-width="50"></v-img>
              </v-col>
              <v-col>
                <v-img src="https://i.imgur.com/BiXsNgt.png" aspect-ratio="1" max-width="50"></v-img>
              </v-col>
              <v-col>
                <v-img src="https://i.imgur.com/dw77URG.png" aspect-ratio="1" max-width="50"></v-img>
              </v-col>
              <v-col></v-col>
            </v-row>
          </div>
        </v-col>

        <v-col
          cols="8"
        >
          <v-row v-if="org">
            <v-col cols="8" align="left">
              <h1>{{ creator }}</h1><br>
              <!-- Intro -->
              <v-row>
                <v-col>
                  <h3>About</h3>
                </v-col>
                <v-col align="right">
                  <v-dialog v-model="dialog" v-if="validCreator" persistent max-width="600px">
                    <template v-slot:activator="{ on }">
                      <v-icon
                        small
                        class="mr-2"
                        v-on="on"
                      >
                        mdi-border-color
                      </v-icon>
                    </template>
                    <v-card>
                      <v-card-title>
                        <span class="headline">Edit Intro</span>
                      </v-card-title>
                      <v-card-text>
                        <v-container>
                          <v-row>
                            <v-col cols="12">
                              <v-form
                                ref="form"
                                v-model="valid"
                                :lazy-validation="lazy"
                              >
                                <v-text-field
                                  v-model="intro"
                                  :counter="500"
                                  :rules="introRules"
                                  label="Intro"
                                  required
                                ></v-text-field>
                              </v-form>
                            </v-col>
                          </v-row>
                        </v-container>
                      </v-card-text>
                      <v-card-actions>
                        <v-spacer></v-spacer>
                        <v-btn color="blue darken-1" text @click="cancel">Cancel</v-btn>
                        <v-btn color="blue darken-1" text @click="edit">Save</v-btn>
                      </v-card-actions>
                    </v-card>
                  </v-dialog>
                </v-col>
              </v-row>
              <v-row>
                <v-col>{{ org['_intro'] }}</v-col>
              </v-row>
            </v-col>
          </v-row>
          <v-row>
              <v-col cols="4" v-if="validCreator">
              <v-btn class="ma-2"
                @click="createEvent"
                color="grey lighten-3"
              >
                Create Event
              </v-btn>
            </v-col>
            <v-col cols="2">
              <v-btn class="ma-2" text icon color="blue darken-1">
                <v-icon
                  @click="incrementLike"
                  large
                >
                  mdi-thumb-up
                </v-icon>
              </v-btn>
              {{ likeCount }}
            </v-col>

            <v-col cols="2">
              <v-btn class="ma-2" text icon color="red darken-2">
                <v-icon
                  @click="incrementDislike"
                  large
                >
                  mdi-thumb-down
                </v-icon>
              </v-btn>
              {{ dislikeCount }}
            </v-col>
          </v-row>
        </v-col>
      </v-row>

      <v-row>
          <v-col>
            <v-card v-if="validCreator" class="elevation-1">
                  <v-row>
                    <v-col
                    class="align-center">
                      <v-textarea
                        name="Reply"
                        :class="['pt-2', 'pl-5', 'pr-5']"
                        :label=self
                        v-model="postContent"
                        auto-grow outlined
                        rows="5"
                        append-icon="mdi-send"
                        @click:append="storePost()"
                        placeholder="Post something..."
                      ></v-textarea>
                    </v-col>
                  </v-row>
            </v-card>
            <div v-if="validCreator" :class="['pt-10']"></div>
            <v-list color="transparent">
                <v-list-group no-action
                  v-for="(post, i) in posts.slice().reverse()"
                  :key="i"
                >
                  <template v-slot:activator>
                    <div :class="['pt-5', 'pb-5']">         
                    <v-card class="elevation-1 text-left justify-center 
                    align-center"
                    :class="['pl-5', 'pr-5']"
                    width="600"
                    >
                      <v-card-title>
                        <v-icon
                          large
                          left
                        >
                          mdi-coin
                        </v-icon>
                        <span class="title font-weight-light">Fundraising DApp</span>
                      </v-card-title>
                      <v-card-text class="headline font-weight-bold">
                        {{post[0]}}
                      </v-card-text>
                      <v-card-actions>
                        <v-list-item class="grow">
                          <v-list-item-avatar color="grey darken-3">
                            <v-img
                              class="elevation-1"
                              src="https://avataaars.io/?avatarStyle=Transparent&topType=ShortHairShortCurly&accessoriesType=Prescription02&hairColor=Black&facialHairType=Blank&clotheType=Hoodie&clotheColor=White&eyeType=Default&eyebrowType=DefaultNatural&mouthType=Default&skinColor=Light"
                            ></v-img>
                          </v-list-item-avatar>

                          <v-list-item-content>
                            <v-list-item-title>{{creator}}</v-list-item-title>
                          </v-list-item-content>
                        </v-list-item>
                      </v-card-actions>
                    </v-card>
                    </div>
                  </template>
                  <v-divider inset></v-divider>
                  <v-list-item round
                    v-for="(reply, j) in post.slice(1)"
                    :key="`${reply}${j}`"
                  >
                    <v-list-item-content>
                      <v-row>
                        <v-col class="text-left justify-center align-center">
                        <h5>{{reply.slice(0, 42)}}</h5>
                        <p>
                          {{reply.slice(42)}}
                        </p>
                        </v-col>
                      </v-row>
                      <v-divider></v-divider>
                    </v-list-item-content>
                  </v-list-item>
                  <v-list-item
                  >
                    <v-list-item-content>
                      <v-row>
                        <v-col>
                          <v-textarea
                            name="Reply"
                            :label=self
                            v-model="replyContent"
                            rounded filled
                            auto-grow
                            rows="1"
                            append-icon="mdi-send"
                            @click:append="storeReply(i)"
                            placeholder="Type a reply..."
                          ></v-textarea>
                        </v-col>
                      </v-row>
                    </v-list-item-content>
                  </v-list-item>
                  </v-list-group>
              </v-list>
          </v-col>

          <v-col>
            <v-card class="elevation-1 text-left justify-center 
                    align-center"
                    :class="['pl-5', 'pr-5']"
                    width="400">
            <v-card-title>Ongoing Events</v-card-title>
            <v-list class="elevation-0" v-if="eventReady">
              <v-list-item-group>
                <v-list-item
                  v-for="(event, i) in ongoingEvents"
                  :key="i"
                  @click="visitEventPage(event['_eventAddress'])"
                >
                  <v-list-item-content>
                      {{ event['_eventName'] }}
                  </v-list-item-content>
                </v-list-item>
              </v-list-item-group>
            </v-list>
            </v-card>
            <div :class="['pt-10']"></div>
            <v-card class="elevation-1 text-left justify-center 
                    align-center"
                    :class="['pl-5', 'pr-5']"
                    width="400">
            <v-card-title>Past Events</v-card-title>
            <v-list class="elevation-0" v-if="eventReady">
              <v-list-item-group>
                <v-list-item
                  v-for="(event, i) in pastEvents"
                  :key="i"
                  @click="visitEventPage(event['_eventAddress'])"
                >
                  <v-list-item-content>
                      {{ event['_eventName'] }}
                  </v-list-item-content>
                </v-list-item>
              </v-list-item-group>
            </v-list>
            </v-card>
          </v-col>
      </v-row>
    </v-container>
  </div>
</template>

<script>
// @ is an alias to /src

export default {
  name: 'Company',
  props: {
    userName: String,
    creator: String,
    loginStatus: Boolean,
    state: Object
  },
  data: () => ({
    self: null,
    select: 0,
    ongoingEvents: [],
    pastEvents: [],
    org: null,
    orgId: null,
    posts: [],
    postContent: "",
    postCount: 0,
    replyContent: "",
    validCreator: false,
    eventReady: false,
    likeCount: 0,
    dislikeCount: 0,
    // for intro
    valid: true,
    lazy: false,
    dialog: false,
    intro: "",
    introRules: [
      v => !!v || 'Intro is required',
      v => (v && v.length <= 500) || 'Intro must be less than 500 characters',
    ],
  }),
  methods: {
    createEvent(){
      this.$router.push({name: 'Create', params: {loginStatus: this.loginStatus, creator: this.userName, 
        state: this.state}})
    },
    visitEventPage(eventAddress){
      this.selectEvent = eventAddress
      this.$router.push({name: 'Account', params: {eventAddress: this.selectEvent, loginStatus: this.loginStatus,
        userName: this.userName, state: this.state}})
    },
    checkCreator(){
      if (this.userName === this.creator){
        this.validCreator = true;
      }
    },
    async edit() {
      if (this.$refs.form.validate()) {
        this.dialog = false;
        await this.state.contract.methods.editOrgIntro(this.orgId, this.intro).send({from: this.self});
        this.org = await this.state.contract.methods.orgList(this.orgId).call({from:this.self});
      }
    },
    cancel() {
      this.dialog = false;
      this.intro = this.org['_intro'];
    },
    async getOrgNPosts(){
      this.orgId = await this.state.contract.methods.creatorName2OrgId(this.creator).call({from: this.self}) - 1;
      this.org = await this.state.contract.methods.orgList(this.orgId).call({from:this.self});
      this.getPosts();
      this.intro = this.org['_intro'];
      this.likeCount = this.org['_likes'];
      this.dislikeCount = this.org['_dislikes'];
    },
    async getEventList(){
      this.ongoingEvents = []
      let len = await this.state.contract.methods.getEventListLength().call({from: this.self});
      for (let i = 0; i < len; i++) {
        let e = await this.state.contract.methods.eventList(i).call({from: this.self});
        if (e['_creator'] === this.creator) {
          if (e['_ongoing'] === true) {
            this.ongoingEvents.push(e);
          }
          else {
            this.pastEvents.push(e);
          }
        }
      }
      this.eventReady = true;
    },
    async getPosts() {
      this.postCount = await this.state.contract.methods.getPostCount(this.orgId).call({from:this.self});
      for (let i = 0; i < this.postCount; i++){
        let p = await this.state.contract.methods.getPostsbyIdx(this.orgId, i).call({from:this.self});
        this.posts.push(p);
      }
    },
    async storePost() { // store new post into contract
      if (this.postContent){
        await this.state.contract.methods.updatePost(this.orgId, this.postContent).send({from: this.self});
        this.updatePosts();
        this.postContent = "";
      }
    },
    async updatePosts() { // update frontend this.posts list
      let newCount = await this.state.contract.methods.getPostCount(this.orgId).call({from:this.self});
      for (let i = this.postCount; i < newCount; i++){
        let p = await this.state.contract.methods.getPostsbyIdx(this.orgId, i).call({from:this.self});
        this.posts.push(p);
      }
      this.postCount = newCount;
    },
    async storeReply(reversePIdx){ // store reply into contract and update frontend
      let pIdx = this.posts.length - reversePIdx - 1
      if (this.posts[pIdx].length < 10 && this.replyContent){
        await this.state.contract.methods.updatePostReply(this.orgId, pIdx, this.self + this.replyContent).send({from: this.self});
        this.posts[pIdx].push(this.self + this.replyContent);
        this.replyContent = "";
      } else {
        alert("Reply count has reached max amount!")
      }
    },
    async incrementLike() {
      await this.state.contract.methods.updateOrgLikes(this.orgId, +this.likeCount+1).send({from: this.self});
      this.likeCount = +this.likeCount+1;
    },
    async incrementDislike() {
      await this.state.contract.methods.updateOrgDislikes(this.orgId, +this.dislikeCount+1).send({from: this.self});
      this.dislikeCount = +this.dislikeCount+1;
    },
  },
  watch: {
    creator: {
      handler() {
        this.self = this.state.accounts[0];
        this.eventReady = false;
        this.getOrgNPosts();
        this.getEventList();
        this.checkCreator();
      },
      immediate: true,
    }
  },
}
</script>