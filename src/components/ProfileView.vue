<template>
    <div class="container" @click.stop="">
        <header v-el:header :style="{ background: 'linear-gradient(transparent, transparent, rgba(0, 0, 0, 0.38)), url(' + avatar + ') center / cover' }" @click="uploadAvatar">
            <span class="title">{{ user.nickname }}</span>
        </header>
        <main>
            <div>
                <i class="material-icons">format_quote</i>
                <div class="form-group">
                    <input placeholder="签名" :value="user.signature" class="form-control" v-model="signature">
                </div>
            </div>
        </main>
        <footer>
            <button class="btn btn-raised btn-primary" @click="save">保存</button>
            <button class="btn btn-primary" @click="close">取消</button>
        </footer>
        <form v-el:form>
            <input v-el:upload name="file" type="file" class="upload" @change="onAvatarUploadChanged">
        </form>
    </div>
</template>

<style scoped lang="less">
    @import "../material-colors";

    .container {
        display: flex;
        width: 400px;
        height: 80%;
        padding: 0;
        margin: 0;
        flex-direction: column;
        background: @md-white;
    }

    header {
        flex-shrink: 0;
        display: flex;
        height: 240px;
        padding: 16px;
        flex-direction: column;
        justify-content: flex-end;
    }
    header > .title {
        margin: 0 0 12px 56px;
        font-size: 34px;
        color: @md-white;
    }

    .btn-fab {
        align-self: flex-end;
        margin: -28px 16px -28px 0 !important;
        background: @md-blue-500 !important;
        color: white !important;
    }

    main {
        flex-grow: 1;
        display: flex;
        padding: 16px;
        flex-direction: column;
        overflow-y: auto;
    }
    main > div {
        display: flex;
        min-height: 56px;
        align-items: center;
    }
    main > div > i {
        flex-shrink: 0;
        color: @md-dark-hint;
    }
    main > div > span {
        flex-grow: 1;
        margin-left: 32px;
    }
    .form-group {
        flex-grow: 1;
        margin: 0 0 0 32px;
    }
    .form-control {
        margin: 0 !important;
    }
    main > div > span.hint {
        color: @md-dark-hint;
    }

    footer {
        display: flex;
        flex-direction: row-reverse;
        height: 56px;
        padding: 0 16px;
    }
    footer > .btn-raised {
        margin-left: 8px;
    }
    footer > .delete {
        margin-right: auto;
        color: @md-red-500;
    }

    .upload {
        display: none;
    }
</style>

<script>
    import EventBus from '../eventbus'
    import Store from '../store'
    import Xhr from '../xhr'

    export default{
        data() {
            return {
                state: Store.state,
                avatar: "",
                nickname: "",
                signature: ""
            };
        },
        computed: {
            user() {
                return this.state.user || {};
            }
        },
        methods: {
            uploadAvatar() {
                this.$els.upload.click();
            },
            onAvatarUploadChanged(event) {
                Xhr.post('upload', this.$els.form)
                        .then((file) => this.avatar = file.path);
            },
            save() {
                Store.updateProfile(this.avatar, this.nickname, this.signature)
                        .then(() => EventBus.$emit('chat-updated'))
                        .then(this.close);
            },
            close() {
                EventBus.$emit('hide-profile');
            }
        },
        watch: {
            'state.user'() {
                this.avatar = this.state.user.avatar;
                this.nickname = this.state.user.nickname;
                this.signature = this.state.user.signature;
            }
        }
    }
</script>
