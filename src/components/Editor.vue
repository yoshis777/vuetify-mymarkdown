<template>
  <v-app>
    <v-navigation-drawer
            fixed
            :mini-variant="miniVariant"
            :clipped="clipped"
            v-model="drawer"
            app
    >
      <v-btn class="addMemoBtn" @click="addMemo">メモの追加</v-btn>
      <v-list>
        <v-list-tile
                value="true"
                v-for="(memo, index) in memos"
                :key="index"
                @click="selectMemo(index)"
                :data-selected="index === selectedIndex"
        >
          <v-list-tile-action>
            <v-icon v-html="memo.icon"></v-icon>
          </v-list-tile-action>
          <v-list-tile-content>
            <v-list-tile-title v-text="displayTitle(memo.markdown)"></v-list-tile-title>
          </v-list-tile-content>
        </v-list-tile>
      </v-list>
    </v-navigation-drawer>
    <v-toolbar fixed app :clipped-left="clipped">
      <v-toolbar-side-icon @click.stop="drawer = !drawer"></v-toolbar-side-icon>
      <v-btn icon @click.stop="miniVariant = !miniVariant">
        <v-icon v-html="miniVariant ? 'chevron_right' : 'chevron_left'"></v-icon>
      </v-btn>
      <v-btn icon @click.stop="clipped = !clipped">
        <v-icon>web</v-icon>
      </v-btn>
      <v-toolbar-title v-text="title"></v-toolbar-title>
      <v-spacer></v-spacer>
      <p v-if="!!user">{{user.displayName}}<br>でログイン中</p>
      <v-btn color="#ccc" @click="logout">ログアウト</v-btn>
      <v-btn icon @click.stop="rightDrawer = !rightDrawer">
        <v-icon>menu</v-icon>
      </v-btn>
    </v-toolbar>
    <v-content>
      <v-container fluid grid-list-md>
        <v-slide-y-transition mode="out-in">
          <v-layout row align-center>
            <v-flex xs6>
              <v-textarea
                      outline
                      name="input-7-4"
                      label="Writing Space"
                      value="The Woodman set to work at once, and so sharp was his axe that the tree was soon chopped nearly through."
                      v-model="memos[selectedIndex].markdown"
              ></v-textarea>
            </v-flex>
            <v-flex xs6>
              <p>Markdown Preview</p>
              <v-container
                      v-html="preview()"
              ></v-container>
            </v-flex>
            <blockquote>

            </blockquote>
          </v-layout>
        </v-slide-y-transition>
      </v-container>
    </v-content>
    <v-navigation-drawer
            temporary
            :right="right"
            v-model="rightDrawer"
            fixed
    >
      <v-list>
        <v-list-tile @click.native="right = !right">
          <v-list-tile-action>
            <v-icon>compare_arrows</v-icon>
          </v-list-tile-action>
          <v-list-tile-title>Switch drawer (click me)</v-list-tile-title>
        </v-list-tile>
      </v-list>
    </v-navigation-drawer>
    <v-footer :fixed="fixed" app>
      <span>&copy; 2017</span>
    </v-footer>
  </v-app>
</template>

<script>
  import marked from "marked";
  export default {
    name: "editor",
    props: ["user"],
    data() {
      return {
        clipped: false,
        drawer: true,
        fixed: false,
        items: [
            { icon: 'bubble_chart', title: 'Inspire' }
        ],
        miniVariant: false,
        right: true,
        rightDrawer: false,
        title: 'エディター画面',
        memos: [
          {
            markdown: ""
          }
        ],
        selectedIndex: 0
      };
    },
    created: function() {
      firebase
        .database()
        .ref("memos/" + this.user.uid)
        .once("value")
        .then(result => {
          if(result.val()){//DBに情報がある場合のみmemosにセットする
            this.memos = result.val();
          }
        })
    },
    mounted: function() {
      document.onkeydown = e => {
        if(e.key === "s" && (e.metaKey || e.ctrlKey)){
          this.saveMemos();
          return false;
        }
      }
    },
    beforeDestroy: function() {
      document.onkeydown = null;
    },
    methods: {
      logout: function() {
        firebase.auth().signOut();
      },
      addMemo: function() {
        this.memos.push({
          markdown: "無題のメモ"
        });
      },
      deleteMemo: function(){
        this.memos.splice(this.selectedIndex, 1);
        if(this.selectedIndex > 0){
          this.selectedIndex--;
        }
      },
      saveMemos: function() {
        firebase
          .database()
          .ref("memos/" + this.user.uid)
          .set(this.memos)
      },
      selectMemo: function(index) {
        this.selectedIndex = index;
      },
      preview: function() {
        return marked(this.memos[this.selectedIndex].markdown);
      },
      displayTitle: function(text){
        return text.split(/\n/)[0]
      }
    }
  }
</script>
<style lang="scss" scoped>
  // scoped:このコンポーネント内でのみ、以下のCSSが有効
  .editorWrapper {
    display: flex;
  }
  .memoListWrapper{
    width: 20%;
    border-top: 1px solid #000;
  }
  .memoList {
    padding: 10px;
    box-sizing: border-box;
    text-align: left;
    border-bottom: 1px solid #000;
    &:nth-child(even) {
      background-color: #eee;
    }
    &[data-selected="true"] {
      background-color: #ccf;
    }
  }
  .memoTitle {
    height: 1.5em;
    margin: 0;
    white-space: nowrap;
    overflow: hidden;
  }
  .addMemoBtn {
    margin-top: 20px;
  }
  .deleteMemoBtn {
    margin: 10px;
  }
  .markdown {
    width: 40%;
    height: 500px;
  }
  .preview {
    width: 40%;
    text-align: left;
  }

</style>
