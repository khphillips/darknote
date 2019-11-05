<template>
    <v-menu v-model="menu" :close-on-content-click="false" :nudge-width="200" offset-x>
        <template v-slot:activator="{ on }">
            <v-badge color="accent">
                <template v-slot:badge>
                    <span v-if="remote_url == null || remote_url == ''">!</span>
                </template>
                <v-icon class="text3--text" v-on="on">fab fa-github</v-icon>
            </v-badge>
        </template>
        <v-card  class="primary darken-1 pa-2">
            <v-card-title>
                <h4 class="text3--text">
                    <v-icon class="text3--text" left>fab fa-github</v-icon>GitHub Settings
                </h4>
            </v-card-title>
            <v-card-text>
                <v-text-field dense v-model="username" label="Username"></v-text-field>
                <v-text-field password dense v-model="password" label="Password"></v-text-field>
                <v-text-field dense v-model="email" label="Email"></v-text-field>
                <v-text-field dense v-model="repo" label="Repo"></v-text-field>
                <v-text-field dense v-model="remote_url" label="Remote Url"></v-text-field>
            </v-card-text>
        </v-card>
    </v-menu>
</template>
<script>
export default {
    props: [],
    data: function() {
        return {
            menu: false,
        }
    },
    mounted: function() {
        if (typeof this.username == 'undefined' || this.username == null){
            //not defined so lets pull from localStorage
            console.log(this.localGitStore);
            if(this.localGitStore != null){
                var ls = JSON.parse(this.localGitStore);
                this.$store.dispatch('gitstore/setUser', ls)
                this.$store.dispatch('gitstore/setRemoteUrl', ls.remote_url)
                this.$store.dispatch('gitstore/setRepo', ls.repo)
            }
        }
    },
    computed: {
        localGitStore : function(){
            return localStorage.getItem('gitStore');
        },
        username: {
            get() {
                return this.$store.state.gitstore ? this.$store.state.gitstore.username : ''
            },
            set(value) {
                this.$store.dispatch('gitstore/setUser', { username: value });
                this.setLocalGitStore();
            }
        },
        email: {
            get() {
                return this.$store.state.gitstore ? this.$store.state.gitstore.email : ''
            },
            set(value) {
                this.$store.dispatch('gitstore/setUser', { email: value })
                this.setLocalGitStore();
            }
        },
        password: {
            get() {
                return this.$store.state.gitstore ? this.$store.state.gitstore.password : ''
            },
            set(value) {
                this.$store.dispatch('gitstore/setUser', { password: value })
                this.setLocalGitStore();
            }
        },
        repo: {
            get() {
                return this.$store.state.gitstore ? this.$store.state.gitstore.repo : ''
            },
            set(value) {
                this.$store.dispatch('gitstore/setRepo', value)
                this.setLocalGitStore();
            }
        },
        remote_url: {
            get() {
                return this.$store.state.gitstore ? this.$store.state.gitstore.remote_url : ''
            },
            set(value) {
                this.$store.dispatch('gitstore/setRemoteUrl', value)
                this.setLocalGitStore();
            }
        }
    },
    methods: {
        setLocalGitStore : function(){
            localStorage.setItem('gitStore', JSON.stringify(this.$store.state.gitstore) )
        }
    }
}
</script>

<style>
	body .v-menu__content .v-card__title h4{
		margin-top: 0;
	}
</style>