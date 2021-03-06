<template lang="pug">
Card.card
    .edit
        FontAwesomeIcon(icon="check", @click="update()")
        router-link(custom, v-slot="{ navigate }", :to="`/user/${uid}`")
            FontAwesomeIcon(icon="arrow-left", @click="back(navigate)")
    .block
        p.section-title.inline 用户ID
        p.inline-item.inline {{ uid }}
    .block
        p.section-title.inline 用户名
        p.inline-item.inline {{ username }}
    .block
        p.section-title.inline 昵称
        TextField.inline-item.inline(v-model:value="nickname", ref="nickname")
    .block
        p.section-title.inline 性别
        Selector.inline-item(:option="sexText", :current="sex", style="width: 10em;", ref="sex")
    .block
        p.section-title.inline 个性签名
        TextField.inline-item.inline(v-model:value="description", style="width: 32em;", ref="description")
    p.section-title 头像
    img.avatar(:src="avatar")
    TextField(v-model:value="avatar", style="width: 32em;", ref="avatar")
    p.section-title 个人主页
    MarkdownEditor.editor(v-model:content="information", ref="information")
</template>

<script>
import Card from '../components/Card.vue';
import Tag from '../components/Tag.vue';
import Button from '../components/Button.vue';
import MarkdownEditor from '../components/MarkdownEditor.vue';
import TextField from '../components/TextField.vue';
import Selector from '../components/Selector.vue';
import config from '../config';
import { sexText } from '../const';
import axios from 'axios';

export default {
    name: 'UserSettings',
    components: {
        Card,
        Tag,
        Button,
        MarkdownEditor,
        TextField,
        Selector
    },
    data: function () {
        return {
            uid: 0,
            avatar: '',
            username: '',
            nickname: '',
            description: '',
            information: ''
        };
    },
    created: async function () {
        this.sexText = sexText;

        await axios.get(`${config.apiServer}/user/detail`, {
            params: {
                uid: this.$cookie.getCookie('hoj_uid')
            }
        }).then(res => {
            this.uid = res.data.data.uid;
            this.nickname = res.data.data.nickname;
            this.username = res.data.data.username;
            this.avatar = res.data.data.avatar;
            this.description = res.data.data.description;
            this.information = res.data.data.information;
            // this.rating = res.data.data.rating;
        });
    },
    methods: {
        update: function () {
            axios.post(`${config.apiServer}/user/update`, {
                nickname: this.nickname,
                avatar: this.avatar,
                description: this.description,
                information: this.information,
            }, {
                headers: {
                    'Authorization': this.$cookie.getCookie('hoj_token')
                }
            }).then(res => {
                this.nickname = res.data.data.nickname;
                this.avatar = res.data.data.avatar;
                this.description = res.data.data.description;
                this.information = res.data.data.information;
            });
        },
        back: function (navigate) {
            this.update();
            navigate();
        },
        uploadData: function () {
            const input = document.createElement('input');
            input.setAttribute('type', 'file');
            input.setAttribute('accept', '.zip');
            input.onchange = event => {
                let formData = new FormData();
                formData.append('pid', this.$route.params.pid);
                formData.append('data', event.target.files[0]);
                axios.post(`${config.apiServer}/problem/upload_data`, formData, {
                    headers: {
                        'Authorization': this.$cookie.getCookie('hoj_token'),
                        'Content-Type': 'multipart/form-data'
                    }
                }).then(res => {
                    console.log(res);
                });
            };
            input.click();
        },
        downloadData: function () {
            
        }
    }
};
</script>

<style scoped>
.card {
    position: relative;
}

.inline {
    display: inline-block !important;
    margin-right: 1em;
    margin-bottom: 0;
}

.block {
    display: block;
}

.inline-item {
    position: relative;
    margin-bottom: -1em;
}

.section-title {
    font-weight: bold;
    font-size: 120%;
}

.editor {
    text-align: initial;
    height: 500px;
    border: solid 1px #efefef;
}

.edit {
    position: absolute;
    right: 0.7em;
    top: 0.7em;
}

.edit > * {
    padding-left: 0.5em;
    transition-delay: 0.3s;
}

.edit > *:hover {
    color: #686868;
}

.avatar {
    width: 6em;
    height: 6em;
    border-radius: 50%;
}

.editor {
    height: 15em;
}
</style>
