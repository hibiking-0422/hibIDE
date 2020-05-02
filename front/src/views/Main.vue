<template>
    <div>
        <div class="cordingArea">
            <!--
            <div class='logoMessage'>
                <span class='logoMessageFont'>Welcome to hibIDE</span>
            </div>
            -->
            <div class='cording-head'>
                <div class='head-select'>
                    <select v-model="selected_compiler">
                        <option v-for="compiler in list" :key="compiler" :value="compiler">{{ compiler }}</option>
                    </select>
                </div>
                <div class='postButton'><button class='posBdesign' @click="postCompile">RUN</button></div>
            </div>

            <div class='cordingPosition'>
                <textarea class="coding" spellcheck="false" @keydown.tab="tab" v-model="code"></textarea>
            </div>
        
            <div class="terminal">
                <!--
                <div class="terminal-title">OUT</div>
                -->
                <textarea class="terminal-area" v-model="results" readonly></textarea>
            </div>
        </div>
        <!--
        <div class='textArea'>
            <div class="chat-area" ref="chatScroll">
                <div v-for="chatMessage in messages" v-bind:key="chatMessage.id">{{ chatMessage }}</div>
            </div>
            <div class="chat-container">
                <input type="text" class="chat-input" v-model='message' v-on:keydown.enter='postChat' />
                <button class="chat-button" v-on:click='postChat'>post</button>
            </div>
        </div>
        
        <select v-model="selected_compiler">
                <option v-for="compiler in list" :key="compiler" :value="compiler">{{ compiler }}</option>
            </select>
            <div class='postButton'><button @click="postCompile">compile</button></div>
            -->
    </div>
</template>

<script>
import io from 'socket.io-client';

export default {
    name: 'Main',
    data: function(){
        return {
            code: 'package main \n\nimport "fmt" \n\nfunc main() { \n\tfmt.Println("Hello, World") \n}',
            results: '>',
            elem: '',
            pos: '',
            val: '',
            message: '',
            messages: [],
            socket : io('localhost:4000'),
             // v-model とバインディングされる
            selected_compiler: 'go-head',
            // select に入れる年を入れる配列
            list: ['clang-head-c', 'gcc-head', 'openjdk-head', 'go-head', 'nodejs-head', 'php-head', 'cpython-head', 'ruby-head', 'rust-head', 'scala-2.13.x', 'swift-head', 'ghc-head', 'vim-head']
        }
    },
    created() {
        this.socket.on('chat_from_server', (txt) => {
            console.log(txt);
            this.messages.push(txt);
        })
    },
    updated() {
        this.scrollToEnd()
    },
    methods: {
        postChat: function() {
            this.socket.emit('chat_from_front', this.message);
        },
        scrollToEnd() {
            this.$nextTick(() => {
                const chatLog = this.$refs.chatScroll
                if (!chatLog) return
                chatLog.scrollTop = chatLog.scrollHeight
            });
        },
        postCompile: function() {
        this.axios.get('http://localhost:9050/api/api.php', {params: {
            code: this.code,
            compiler: this.selected_compiler
        }}).then(response =>{
            this.results += ' ' + response.data + '>';
            console.log(response.data);
        });
        },
        tab: function(event) {
            event.preventDefault();
            this.elem = event.target;
            this.pos = this.elem.selectionStart;
            this.val = this.elem.value;
            this.elem.value = this.val.substr(0, this.pos) + '\t' + this.val.substr(this.pos, this.val.length);
            this.elem.setSelectionRange(this.pos + 1, this.pos + 1);
        }
    }
}
</script>

<style scoped>
select{
  background:black;
  color: white;
}

.posBdesign {
    color: white;
    background-color: #000000;
}

.cording-head {
    color: white;
}

.head-select {
    display: inline;
}

.cordingArea {
    width: 80%;
    margin: 0 0 0 auto;
    margin-top: 25px;
    position: relative;
    top: -25px;
    border: solid 1px;
    height: 300px;
}

.coding {
    width: 100%;
    height: 840px;
    font-size: 2.0em;
    color: white;
    outline: none;
    background-color:#000000;
}

.cordingPosition {
    width: 100%;
    text-align: center;
}

.terminal {
    width: 100%;
    margin-right: auto;
    margin-left: auto;
    height: 100%;
    position: relative;
    top: -10px;
}

.terminal-pos {
    height: 100%;
}

.terminal-title {
    text-align: left;
    background-color: black;
    width: 50px;
    color: white;
    letter-spacing: 0.3em;
    padding-left: 10px;
    padding-right: 5px;
    border: double 4px #333;
}

.terminal-area {
    font-family: 'courier new', Futura, Helvetica, '游ゴシック', 'メイリオ', Osaka;
    width: 99.5%;
    height: 300px;
    overflow: auto;    /* IEでは設定しておかないとスクロールバーが表示されしまう */
    display: block;    /* 指定しないとスクロールバーが表示される */
    border: none;    /* 変な余白が出ないように */
    background-color: black;
    color: white;
    font-size: 12pt;
    line-height: 1.4em;    /* 全角文字を入力時にこれ以上の高さがないと入力時にぎくしゃくするため */
    border: double 4px #333;
}

.postButton {
    width: 100px;
    margin-right: auto;
    margin-left: auto;
    display: inline;
}

.logoArea {
    width: 500px;
    margin-right: auto;
    margin-left: auto;
}

.logo{
    text-align: center;
}

.logoMessage {
    text-align: center;
    margin-top: 25px;
    margin-bottom: 25px;
}

.logoMessageFont {
    font-size: 25px;
}

.formArea {
    width: 50%;
    margin-right: auto;
    margin-left: auto;
    border: solid 1px;
    text-align: center;
    margin-bottom: 50px;
}

.chat-area {
  width: 100%;
  height: 44.5vh;
  border: solid green;
  border-radius: 10px;
  position: relative;
  overflow:scroll;
  overflow-x: hidden;
  margin-right: auto;
  margin-left: auto;
}

.chat-container {
  width: 100%;
}

.chat-input {
  width: 70%;
}
.chat-button {
  width: 26%;
}
.textArea {
    width: 15%;
    position:fixed;
    right:  10px;                /* 右からの位置指定 */
    top: 10px;  
}

</style>