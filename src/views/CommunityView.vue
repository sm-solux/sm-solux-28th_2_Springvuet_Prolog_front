<template>
  <LeftMenu/>

  <div class ="container">
    <div id="back">
      <div class="top">
        <div class="select">
        <h2>모집 커뮤니티</h2>
          <select v-model="partModel" class="part-select" v-bind:id="input_id" v-on:input="updateValue($event.target.value)"> 
            <option v-for="(item, index) in parts" :key="index">{{ item }}</option>
          </select>
          <select v-model="stateModel" class="state-select"> 
            <option v-for="(item, index) in states" :key="index">{{ item }}</option>
          </select>
          <SelectBox v-model="preselect_value" :items="somethings" :input_id="'my_selectbox'" @input="value => { preselect_value = value }" />
        </div>

      <div class="new-post">
      <div class="bSelect">
        <select v-model="partModel2" class="part-select2" v-bind:id="input_id" v-on:input="updateValue($event.target.value)"> 
          <option v-for="(item, index) in parts_input" :key="index">{{ item }}</option>
        </select>
        <select v-model="stateModel2" class="state-select2"> 
          <option v-for="(item, index) in states_input" :key="index">{{ item }}</option>
        </select>
        <SelectBox
          v-model="preselect_value" :items="somethings" :input_id="'my_selectbox'" @input="value => { preselect_value = value }"
        ></SelectBox>
      </div>
      <div class="bInput">
        <input type="text" id="title1" class="write-title" v-model="inputTitle_community" placeholder="제목을 작성하세요">
        <div class="text-form">
          <textarea type="text" id="body1" class="write-body" v-model="inputBody_community" @keydown="handleKeyDown" maxlength="200" @input="checkLength" placeholder="본문은 200자, 3줄까지 작성 가능합니다" />
          <div class="textCount">{{ textCount }}</div>
        </div>
      </div>
      <div class="bBtn">
        <ButtonComponent id="btn1" parameter="community" @click="addNewPost" msg="등록하기"/>
      </div>
      </div>

      </div>

      <div class="bottom">
            <!--기존 게시글 보이는 부분-->
        <div v-for="(item, index) in currentPosts" :key="index">
          <div v-if="(partModel === '전체' || partModel === item.postCategory) && (stateModel === '전체' || stateModel === item.postStatus)">
          <!-- <div v-if="partModel===item.data.post_part && stateModel===item.data.post_state"> -->
            <div class="posts">
              <div class="posts-part-state">
                <div class="post-part">{{ item.postCategory }}</div>
                <div class="post-state">{{ item.postStatus }}</div>
              </div>
              <div class="posts-contents">
                <div class="post-title">{{ item.postTitle }}</div>
                <div style="white-space:pre;" class="post-body">{{ item.postContent }}</div>
              </div>
              <div class="posts-right">

                <!-- 수정하기 모달창-->
                <div class="modifying">
                  <div class="modifying-icon">
                    
                  </div>
                  <Button class="modifying-button" @click="modalOpen_modify(index)">수정하기</Button>

                  <div class="modal-wrap" v-show="modalCheck_modify">
                    <div class="modal-container">
                      <div class="flex-box">
                        <div class="modal-info-modify">
                          <div class="post-title-modify">
                            <label class="post-title-label">수정하기</label>
                          </div>
                          <div class="select-container-modify">
                              <select
                                v-model="currentPosts[modalIndex_modify].postCategory"
                                class="part-select2"
                                v-bind:id="input_id"
                                v-on:input="updateValue($event.target.value)"
                              > 
                                <option v-for="(item, index) in parts_input" :key="index">{{ item }}</option>
                              </select>
                              <select
                                v-model="currentPosts[modalIndex_modify].postStatus"
                                class="state-select2"
                              > 
                                <option v-for="(item, index) in states_input" :key="index">{{ item }}</option>
                              </select>
                              <SelectBox
                                v-model="preselect_value"
                                :items="somethings"
                                :input_id="'my_selectbox'"
                                @input="value => { preselect_value = value }"
                              ></SelectBox>
                          </div>
                          <div class="input-container-modify">
                            <input type="text" class="write-title-modify" v-model="currentPosts[modalIndex_modify].postTitle"/>
                            <textarea type="text" class="write-body-modify" v-model="currentPosts[modalIndex_modify].postContent"/>
                          </div>
                          <div class="botton-container-modify">
                              <div class="btn-container-right">
                                <ButtonComponent parameter="community" msg="저장하기" @click="saveBtn"/>
                              </div>
                              <div class="modal-btn">
                                <ButtonComponent parameter="community" msg="취소하기" class="close-btn" @click="modalOpen_modify(index)"/>
                                <!-- <ButtonComponent parameter="community" msg="취소하기" class="close-btn" @click="modalOpen(index)"/> -->
                              </div>
                          </div>
                          
                          <!-- </div> -->

                          <!-- <>수정하기</h5>
                            <div class="project-date">
                                  <div class="edit-title">
                                      <input msg="{{ item.data.post_title }}"  >
                                  </div>
                                  <div class="edit-body">
                                      
                                  </div>
                              </div> -->   
                      </div>
                    </div>
                  </div>
                </div>        
              </div>
              
              <!-- 수정하기 모달창 끝 -->
              <!-- 삭제하기 -->
              <Button class="delete-button" @click="deletePost(index)">삭제하기</Button>

                <div class="post-info">
                  <div class="post-writer"> 작성자 : {{ item.nickname }}</div>
                  <div class="post-date"> 등록일 : {{ item.createAt }}</div>
                </div>
              </div>
            
              <!-- 댓글달기 모달창-->
              <div class="comments">
                <div class="comment-icon">
                  <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M7.5 8.25h9m-9 3H12m-9.75 1.51c0 1.6 1.123 2.994 2.707 3.227 1.129.166 2.27.293 3.423.379.35.026.67.21.865.501L12 21l2.755-4.133a1.14 1.14 0 0 1 .865-.501 48.172 48.172 0 0 0 3.423-.379c1.584-.233 2.707-1.626 2.707-3.228V6.741c0-1.602-1.123-2.995-2.707-3.228A48.394 48.394 0 0 0 12 3c-2.392 0-4.744.175-7.043.513C3.373 3.746 2.25 5.14 2.25 6.741v6.018Z" />
                  </svg>
                </div>
                <Button class="comment-button" @click="modalOpen(index)">댓글 달기</Button>

                <div class="modal-wrap" v-show="modalCheck">
                  <div class="modal-container">
                    <div class="flex-box">
                      <div class="modal-info2">
                        <div class="post-title2" v-if="postDetail"> {{ postDetail.postTitle }} </div>
                        <div class="post-body2" v-if="postDetail" style="white-space:pre;">{{ postDetail.postContent }}</div>
                        <div class="post-info2">
                          <div class="post-writer" v-if="postDetail"> 작성자 : {{ postDetail.nickname}}</div>
                          <div class="post-date" v-if="postDetail"> 등록일 : {{ (postDetail.createAt[0]) + "-" + String(postDetail.createAt[1]).padStart(2, '0') + "-" + String(postDetail.createAt[2]).padStart(2, '0') }}</div>
                        </div>

                        <div class="modal-btn-comments">
                          <div class="close-btn" @click="modalClose">
                            <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-1 h-1">
                              <path stroke-linecap="round" stroke-linejoin="round" d="M6 18 18 6M6 6l12 12" />
                            </svg>
                          </div>
                        </div>

                        <hr class="horizontal-divider" style="width: 100%;">
                        <div class="container-modal-window">
                          <div class="written-comments" v-for="comment in comments" :key="comment.commentUuid">
                              <div class="writer-id"> {{ comment.nickname }}
                                <div class="delete-comment">
                                  <div class="close-btn" @click="deleteComment(index)">
                                    <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="white" class="w-1 h-1">
                                      <path stroke-linecap="round" stroke-linejoin="round" d="M6 18 18 6M6 6l12 12" />
                                    </svg>
                                  </div>
                                </div>
                              </div>
                              <div class="written-text"> {{ comment.content }}</div>
                              <div class="post-date"> 작성일 : {{ (comment.createAt[0]) + "-" + String(comment.createAt[1]).padStart(2, '0') + "-" + String(comment.createAt[2]).padStart(2, '0') }}</div>
                          </div> 
                        </div>

                        <div class="input-container">
                          <input type="text" id="write-comment" v-model="inputComment" placeholder="댓글 작성하기" class="input-long" v-on:keyup.enter="addNewComment"/>
                          <button @click="addNewComment" class="send-icon">
                            <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-1 h-1">
                              <path stroke-linecap="round" stroke-linejoin="round" d="M6 12 3.269 3.125A59.769 59.769 0 0 1 21.485 12 59.768 59.768 0 0 1 3.27 20.875L5.999 12Zm0 0h7.5" />
                            </svg>
                          </button>
                        </div>
                    </div>
                  </div>
                </div>
              </div>    
            </div>
          </div>   
        </div> 
      </div>
          <!-- 댓글달기 모달창 끝 -->
      </div>
    </div>
  </div>
</template>

<style scoped>
.modal-info-modify{
  display:flex;
  flex-direction: column;
}
.select-container-modify{
  display:flex;
  flex-direction: row;
}
.botton-container-modify{
  display:flex;
  flex-direction: center;
}
.container{padding: 20px 90px}
#back{
  /* margin-left: 10px; */
  display: flex;
  flex-direction: column;
  line-height:2.0;
  align-items: center;
  justify-content: flex-start;
  /* display:grid;
  grid-template-rows: 400px 600px; */
}
.top{
  display: flex;
  position: fixed;
  flex-direction: column;
  z-index: 100;
}

.bottom{
  margin-top: 40vh;
  padding: 5px 0px;
  height: 50vh;
  width: 80vw;
}

.select{
  display: flex;
  align-items: center;
}

.bottom{
  overflow-y: scroll;
}

.part-select{
  background-color: #B1B2FF;
  margin-top: 8px;
  margin-left: 30px;
  padding-left: 10px;
  width: 90px; 
  height: 40px;
  border-radius: 30px;
  border: none;
  color: white;
  font-size: 14pt;
}
.state-select{
  background-color: #B1B2FF;
  margin-top: 8px;
  margin-left: 30px;
  padding-left: 10px;
  width: 130px; 
  height: 40px;
  border-radius: 34px;
  border: none;
  color: white;
  font-size: 14pt;
}
.part-select2 {
  position: absolute;
  background-color: #B1B2FF;
  margin-top: 25px; 
  margin-left: 20px;
  padding-left: 10px;
  width: 90px; 
  height: 40px;
  border-radius: 30px;
  border: none;
  color: white;
  font-size: 13pt;
}
.state-select2 {
  position: absolute;
  background-color: #B1B2FF;
  margin-top: 80px;
  margin-left: 20px;
  padding-left: 10px;
  width: 130px;
  height: 40px; 
  border-radius: 30px;
  border: none;
  color: white;
  font-size: 13pt;
}
.new-post{
  background-color: #D2DAFF;
  width: 80vw;
  display: flex; 
  flex-direction: row;
}
.bSelect{
  width: 30px;
  height: 200px;
}
.bInput{
  width: 1000px;
  height: 200px;
}
.bBtn{
  width: 150px;
  height: 200px;
}
.write-title{
  background-color: #B1B2FF;
  padding-inline: 10px;
  width: 770px;
  height: 35px;
  border-width: 0px;
  font-size: 15pt;
  margin-left: 150px;
  margin-top: 25px;
}
.write-body{
  background-color: #B1B2FF;
  margin-left: 150px;
  margin-top: 13px;
  padding-inline: 10px;
  padding-top: 5px;
  width : 770px;
  height: 100px;
  border-width: 0px;
  font-size: 15pt;
  resize:none;
}
.write-body:focus{
  outline: 0;
}
.write-label {
  font-size: 15pt;
  margin-top: 5px;
  margin-bottom: 5px;
}
#btn1{
  margin-top: 130px;
  font-size: 13pt;
}

/* 기존 커뮤니티 글 */
.posts{
  background-color: #D2DAFF;
  margin-top: 30px;
  display: grid;
  width: 1200px;
  grid-template-columns: 185px 700px 320px;
  grid-template-rows: 155px 50px;
}
.post-title {
  font-weight: bold;
  font-size: 14pt;
  padding-top: 20px;
  text-align: left;
}
.post-body{
  text-align: left;
  font-size: 12pt;
}
.posts-right{
  text-align: right;
  padding: 20px 13px 0 0;
}
.comments{
  background-color: #B1B2FF;
  width: 1200px;
  height: 50px;
  display: flex; 
  flex-direction: row;
  color: white;
}
.post-info{
  margin-right: 10px;
  font-weight: bold;
}
.post.title{
  font-size: 27pt;
  line-height:1.0;
  font-weight: bold;
  white-space: nowrap;
}
.post.body{
  font-size: 12pt;
  font-weight: medium;
}
.post-part{
  background-color: #B1B2FF;
  margin-top: 25px; 
  margin-left: 20px;
  width: 75px; 
  border-radius: 30px;
  border: none;
  color: white;
  font-size: 13pt;
  display: flex; 
  align-items: center;
  justify-content: center;
}
.post-state{
  background-color: #B1B2FF;
  margin-top: 10px;
  margin-left: 20px;
  width: 100px; 
  border-radius: 30px;
  border: none;
  color: white;
  font-size: 13pt;
  display: flex; 
  align-items: center;
  justify-content: center;
}

.text-form{
  display: flex;
  align-items: flex-end;
}

/* 댓글달기 모달창 */
.post-title2{
  font-weight: bold;
  font-size: 14pt;
  padding-top: 20px;
  text-align: left;
  margin-left: 10px;
}
.post-body2{
  text-align: left;
  font-size: 12pt;
  margin-left: 10px;
}
.modal-btn-comments{
  position: fixed;
  top: 4vh;
  left: 80vw;
}
.modal-container{
  background-color: #D2DAFF;
  color: black;
  padding-left: 20px;
  overflow: hidden; /*추가*/
  display: flex; /*추가*/
  align-items: center;
}
.input-container{
  display: flex;
  /* align-items: center; */
  height: 40px;
  margin-left: 0px;
  margin: 10px;
  padding: 10px;
}
.label-text{
  width: 10vw;
}
.input-long{
  width: 75vw; /*수정*/
  height: 50px; /*수정*/
  font-size: 13pt;
  border: 0;
  padding-left: 10px;
}
input:focus{
  outline: 0;
}
.comment-button {
  background: none;
  border: none;
  color: white;
  font-size: 13pt;
  font-weight: bold;
  padding-left: 20px;
}
.written-comments {
  background-color: #B1B2FF;
  text-align: left;
  padding: 10px 0 10px 15px;
  width: 90%;
  height: 15vh;
  margin: 16px 0 0 10px; /*추가*/
  border-radius: 5px; /*추가*/
}
.container-modal-window{
  height: 45vh;
  width: 80vw;
  overflow-x: hidden;
  overflow-y: scroll;
  display: flex;
  justify-content: space-between;
  margin-top: 10px;
  flex-direction: column;
  align-items: center;
}
.writer-id{
  font-weight: bold;
  display:flex;
  flex-direction: row;
  justify-content: space-between;
}
.writer-id .delete-comment{
  margin-right: 20px;
}
.send-icon {
  background-color: #B1B2FF;
  width: 50px;
  height: 50px;
  transform: rotate(-90deg);
  border: 0;
}
.comment-icon{
  width: 35px;
  height: 35px;
  padding: 10px 3px 0 15px;
}
.horizontal-divider {
  width: 95%;
  margin: 5px;
}
.write-comment{
  width: 80%;
}
.write-comment::placeholder {
  color: rgba(255, 255, 255, 0.747);
}
.comment-scroll{ /*추가*/
  overflow-y: scroll;
}
.modal-info2{ /*추가*/
  justify-content: flex-start;
}
.post-info2{ /*추가*/
  justify-content: flex-end;
  text-align: right;
  margin-right: 20px;
}


/* 수정하기 모달창 */
.modifying-button {
    background: none;
    border: none;
    color: white;
    font-size: 13pt;
    font-weight: bold;
    text-decoration: underline;
}

.delete-button{
  background: none;
  border: none;
  color: white;
  font-size: 13pt;
  font-weight: bold;
  text-decoration: underline;
}
.post-title-modify{
  display:flex;
  justify-content: space-between;
  font-weight: bold;
  font-size: 30pt;
  margin-left: 15px;
}
.modal-info-modify{
  /* display:flex;
  flex-direction: column; */
  display: grid;
  grid-template-rows: 10vh 20vh 30vh 20vh;
}
.select-container-modify{
  display:flex;
  flex-direction: column;
  /* background-color:black; */
}
.input-container-modify{
  display:flex;
  flex-direction: column;
  align-items: center;
  /* background-color:pink; */
  margin-left: 10px;
}
.write-title-modify{
  background-color: #B1B2FF;
  padding-inline: 10px;
  width: 97%;
  height: 35px;
  border-width: 0px;
  font-size: 15pt;
  /* margin-top: 25px; */
}
.write-body-modify{
  background-color: #B1B2FF;
  margin-top: 13px;
  padding-inline: 10px;
  padding-top: 5px;
  width: 97%;
  height: 100px;
  border-width: 0px;
  font-size: 15pt;
  resize:none;
}
.botton-container-modify{
  display:flex;
  justify-content: center;
}
.textCount{
  margin-left: 10px;
}
</style>

<script>
import api from '@/axios.js';
import { parseYearTime } from '@/utils/date.js';
import LeftMenu from '@/components/LeftMenu.vue'
import ButtonComponent from '@/components/ButtonComponent.vue';

  export default {
    name: 'CommunityView',
    components: {
      LeftMenu,
      ButtonComponent,
    },
    data() {
      return {
        partModel: '전체',
        stateModel: '전체',
        partModel2: '웹',
        stateModel2: '모집중',
        parts: ['전체','웹', '앱', '데분'],
        states:['전체','모집중', '모집 완료'],
        parts_input: ['웹', '앱', '데분'],
        states_input: ['모집중', '모집 완료'],
        message: "글을 입력하세요",
        currentPosts: [],
        // currentPost: null,
        currentComments: [
          // [
          //   {
          //     writer_id:"파송송계란탁",
          //     written_text: "저 참여하고 싶습니다.",
          //     written_date: "2024-02-01"
          //   },
          //   {
          //     writer_id:"우주최강개발자",
          //     written_text: "프로젝트 경험 있으신가요?",
          //     written_date: "2024-02-01"
          //   }
          // ],
          // [
          //   { 
          //     writer_id:"파송송계란탁",
          //     written_text: "안녕하세요~ 어떤 웹페이지인지 설명 부탁드립니다",
          //     written_date: "2024-02-01",
          //   },
          //   { 
          //     writer_id:"나송집가고싶송",
          //     written_text:"추억을 공유하는 웹페이지입니다",
          //     written_date: "2024-02-02"
          //   }
          // ],

        ],
        postDetail: null,
        comments: [],
        modalIndex: 0,
        modalCheck: false,
        modalIndex_modify: 0,
        modalCheck_modify: false,
        inputTitle_community: '',
        inputBody_community:'',
        inputComment: '',
        input_id: null,
        preselect_value: null,
        somethings: null,
      };
    },
    created() {
      api.get('/post')
      .then(response => {
        this.posts = response.data || {} ;  // 응답 데이터를 project에 저장
        for (let post of this.posts) {
          console.log(post)
              this.currentPosts.push({
                  postCategory: post.postCategory,  // scheduleUuid를 id로 사용
                  postStatus: post.postStatus,
                  postTitle: post.postTitle,
                  postContent: post.postContent,
                  nickname: post.nickname,
                  createAt: String(post.createAt[0]) + "-" + String(post.createAt[1]).padStart(2, '0') + "-" + String(post.createAt[2]).padStart(2, '0'),
                  postUuid: post.postUuid
                  //comments_num: 3,
              });
          }
        })
      .catch(error => {
        console.error(error);
          });
    },
    computed: {
      textCount() {
        return this.inputBody_community.length > 0 ? `${this.inputBody_community.length}자` : '0자';
      },
    },
    methods: {
      checkLength() {
        if (this.inputBody_community.length > 200) {
          this.inputBody_community = this.inputBody_community.substring(0, 200);
        }
      },
      updateValue: function(value){
        this.$emit('input', value);
      },
      modalOpen(index) {
        // this.modalCheck = !this.modalCheck;
        // console.log(index);
        // this.modalIndex= index;
        const postUuid = this.currentPosts[index].postUuid;
        api.get(`/post/${postUuid}`)
        .then(response => {
          this.postDetail = response.data.postResponseDto;
          this.comments = response.data.commentResponseDtoList;
          this.modalCheck = true;
          console.log()
        })
        .catch(error => {
          console.error(error);
        });
      },   
      modalClose() {
          this.modalCheck = false;  // 모달 창 닫기
      },
      modalOpen_modify(index) {
        this.modalCheck_modify = !this.modalCheck_modify;
        // console.log(index);
        this.modalIndex_modify = index;
        // const postUuid = this.posts[index].postUuid;
        // api.get(`/post/${postUuid}`)
        // .then(response => {
        //   this.currentPost = response.data;
        //   this.modalCheck = true;
        // })
        // .catch(error => {
        //   console.error(error);
        // });
      },
      deletePost(index){
        this.currentPosts.splice(index, 1);
        this.currentComments.splice(index, 1);
      },
      deleteComment(index){
        this.currentComments[this.modalIndex].splice(index, 1);
      },
      addNewPost() {
        const newPost = {
          postCategory: this.partModel2,  // scheduleUuid를 id로 사용
          postStatus: this.stateModel2,
          postTitle: this.inputTitle_community,
          postContent: this.inputBody_community,
          nickname: this.nickname,
          createAt: new Date().toISOString().slice(0, 10),
        };
        if(this.inputTitle_community && this.inputBody_community){
          this.currentPosts.push(newPost);
          api.post('/post/create', {
            postCategory: this.partModel2,  // scheduleUuid를 id로 사용
            postStatus: this.stateModel2,
            postTitle: this.inputTitle_community,
            postContent: this.inputBody_community,
            nickname: this.nickname,
            createAt: new Date().toISOString().slice(0, 10),
          })
          .then(response => {
              console.log(response);
          })
          .catch(error => {
              console.log(error);
          });
          // 등록 후 입력 필드 초기화 (선택 필드는 초기값으로 되돌리고, 텍스트 필드는 비움)
          this.partModel2 = '웹';
          this.stateModel2 = '모집중';
          this.inputTitle_community = '';
          this.inputBody_community = '';
        }
        else if(!this.inputTitle_community && !this.inputBody_community){
          alert("제목과 내용을 입력하세요");
        }
        else if(!this.inputTitle_community){
          alert("제목을 입력하세요")
        }
        else{
          alert("내용을 입력하세요");
        }
        
      },
      addNewComment() {
        // const newComment = {
        //     writer_id: "새로운 작성자", // 아이디 가져오기 (+작성자일 경우 '작성자' 붙이기)
        //     written_text: this.inputComment,  
        //     written_date: new Date().toISOString().slice(0, 10),  
        // };
        // if(!this.inputComment[this.modalIndex]){
        //   alert("댓글을 입력하세요");
        // }else {
        //   if (!this.currentComments[this.modalIndex]) {
        //     this.currentComments[this.modalIndex] = [];
        //   }
        //   this.currentComments[this.modalIndex].push(newComment);
        //   this.inputComment = ''; 
        // }
        const postUuid = this.postDetail.postUuid;
          api.post(`/post/${postUuid}/comment/create`, {
            content: this.inputComment
          })
            .then(response => {
              // 댓글 작성 성공. 댓글 목록을 새로고침하거나, 현재 댓글을 목록에 추가
              this.comments.push(response.data);
              this.inputComment = '';  // 입력 필드 초기화
              console.log()
            })
            .catch(error => {
              console.error(error);
            });
      },
      saveBtn() {
        this.modalCheck_modify = !this.modalCheck_modify;
      },

      formatYear(when) {
        let {year, month, date} = parseYearTime(when);
        return `${year}-${month}-${date}`;
      },
      handleKeyDown(event) {
        const rows = this.inputBody_community.split('\n').length;
        const maxRows = 3;
        if (rows > maxRows) {
          event.preventDefault();
          const modifiedText = this.inputBody_community.split('\n').slice(0, maxRows);
          this.inputBody_community = modifiedText.join('\n');
        }
      },
    }
  }
</script>