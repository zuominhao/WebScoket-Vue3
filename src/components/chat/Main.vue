<template>

    <div style="height: 30px; width: 250px;border: 0px solid black">
        <div>
            <el-input
            v-model="url"
            type="text"
            placeholder="示例:ws://192.168.158.159:9002"
            @keydown.native.enter="connectWebSocket()"
        ></el-input>
        </div>

        <button @click="disconnectWebSocket()">断开连接</button>
    </div>

    <div ref="messageContainer"
         style="height: 500px; width: 900px; overflow-y: auto; margin: 0 auto; border: 1px solid black">
        <div
            v-for="(item, index) in list"
            :style="{ textAlign: item.align }"
            :key="index"
            style="margin: 20px;"
        >
            <span v-if="item && item.align === 'right'">{{ item.text }}</span>
            <span v-if="item && item.align === 'left'">{{ item.text }}</span>
        </div>
    </div>
    <div class="center-input-div">
        <el-input
            v-model="textarea"
            :rows="1"
            type="textarea"
            placeholder="Please input"
            @keydown.native.enter="sendMessage()"
        ></el-input>
    </div>
</template>

<script>
import WebSocket from 'websocket';

export default {
    data() {
        return {
            websocket: null,
            textarea: "",
            list: [],
            url:"ws://192.168.158.159:9002"
        };
    },
    methods: {
        connectWebSocket() {
           this.websocket = new window.WebSocket(this.url);
            this.websocket.onopen = () => {
                console.log('WebSocket 连接已建立');
                this.$alert('WebSocket 连接已建立', {
                    confirmButtonText: '确定',
                })
            };
            this.websocket.onmessage = (event) => {
                let message = JSON.parse(event.data);
                this.list.push({text: message, align: "left"});
                this.scrollToBottom();
            };
            this.websocket.onerror = (error) => {
                console.error('WebSocket 错误:', error);
            };
            this.websocket.onclose = () => {
                console.log('WebSocket 连接已关闭');
                this.$alert('对方已关闭和您的WebSocket 连接', {
                    confirmButtonText: '确定',
                })
            };
        },

        disconnectWebSocket(){
            if(this.websocket){
                this.websocket.close();
                console.log("websocket 已断开");
                this.$alert('您已关闭和对方的WebSocket 连接', {
                    confirmButtonText: '确定',
                })
            }else{
                console.warn("websocket 不存在");
            }
        },

        sendMessage() {
            let message = this.textarea;
            let userId = localStorage.getItem("currentUserId");
            let targetId = 43;
            let data = {message, userId, targetId};

            if (this.textarea) {
                this.list.push({text: this.textarea, align: "right"});
                this.textarea = "";
                console.log("text >>> ", data);
                this.websocket.send(JSON.stringify(data));
            }
            this.$nextTick(() => {
                this.scrollToBottom();
            });
        },

        scrollToBottom() {
            let container = this.$refs.messageContainer;
            if (container) {
                container.scrollTop = container.scrollHeight;
            }
        }
    },
    mounted() {
        // this.connectWebSocket();
    },
    beforeUnmount() {
        if (this.websocket) {
            this.websocket.close();
        }
    }
};
</script>

<style scoped>
.center-input-div {
    margin: 0 auto;
    width: 900px;
}

</style>
