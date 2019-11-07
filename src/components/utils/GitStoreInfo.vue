<template>
    <v-menu min-height="625px" v-model="menu" :close-on-content-click="false" :nudge-width="200" offset-y>
        <template v-slot:activator="{ on }">
            <v-badge color="darknote darken-1">
                <template v-slot:badge>
                    <v-icon small color="red" v-if="(repo == null || repo == '')">!</v-icon>
                </template>
                <v-icon class="text3--text" v-on="on">fab fa-git-square</v-icon>
            </v-badge>
        </template>
        <v-card  class="darknote darken-1 pa-2">
            <v-card-title>
                <h4 class="text3--text">
                    <v-icon class="text3--text" left>fab fa-git-square</v-icon>Git Settings
                </h4>
            </v-card-title>
            <v-card-text v-if="!newR">
                <v-text-field dense v-model="name" label="Name"></v-text-field>
                <v-text-field dense v-model="email" label="Email"></v-text-field>
                <v-select :items="$store.state.gitstore.repoList" v-model="repo" label="Repository"></v-select>
                <v-btn @click="newRepo">New</v-btn>
                <v-btn @click="load">Pull</v-btn> 
            </v-card-text>
            <v-card-text v-else>
                <v-text-field dense v-model="repo_label" label="Repository Label"></v-text-field>
                <v-text-field dense v-model="remote_url" label="Remote Url"></v-text-field>
                <v-btn color="darknote" @click="save">Save</v-btn> 
                <v-btn v-if="newR" @click="clone">Clone</v-btn>
                <v-btn fab small @click="reset">X</v-btn> 
            </v-card-text>
        </v-card>
        <v-overlay absolute :value="error != null">
            <v-card @click="error = null">
                <v-card-text class="giterror">
                {{ error }}
                </v-card-text>
            </v-card>
        </v-overlay>
    </v-menu>
</template>
<script>
export default {
    props: [],
    data: function() {
        return {
            menu: false,
            repo : null,
            repo_label : null,
            remote_url : null,
            name: null,
            email:null,
            error:null,
            newR : false,
        }
    },
    created: function() {
        if (typeof this.username == 'undefined' || this.username == null){
            //not defined so lets pull from localStorage
            if(this.localGitStore != null){
                var ls = JSON.parse(this.localGitStore);
                this.$store.dispatch('gitstore/setUser', ls)
                this.$store.dispatch('gitstore/loadRepo', ls)
                this.repo = ls.repo;
                this.name = ls.name;
                this.email = ls.email;
            }
        }
    },
    computed: {
        localGitStore : function(){
            return localStorage.getItem('gitStore');
        },
    },
    methods: {
        setLocalGitStore : function(){
            localStorage.setItem('gitStore', JSON.stringify(this.$store.state.gitstore) )
        },
        newRepo : function(){
            this.repo_label = null
            this.remote_url = null
            this.error = null
            this.newR = true;
        },
        reset : function(){
            this.repo = this.$store.state.gitstore ? this.$store.state.gitstore.repo : null
            this.remote_url = this.$store.state.gitstore ? this.$store.state.gitstore.remote_url : null
            this.name = this.$store.state.gitstore ? this.$store.state.gitstore.name : null
            this.email = this.$store.state.gitstore ? this.$store.state.gitstore.email : null
            this.newR = false;
        },
        save : function(){
            this.error = null
            var g = this;
            this.$store.dispatch('gitstore/setUser', { 
                name: this.name,
                email: this.email 
            });
            this.$store.dispatch('gitstore/newRepo', { 
                repo: this.repo_label,
                remote_url: this.remote_url 
            }).then(function(err){
                if (err){
                    g.error = err
                }else{
                    g.repo = g.repo_label;
                    g.newR = false;
                }
            });
            this.$nextTick(this.setLocalGitStore);
        },
        load : function(){
            this.error = null
            var g = this;
            this.$store.dispatch('gitstore/setUser', { 
                username: this.name,
                email: this.email 
            })
            this.$store.dispatch('gitstore/loadRepo', { 
                repo: this.repo,
                remote_url: this.remote_url 
            }).then(function(err){
                if (err){
                    g.error = err
                }
            });
            this.$nextTick(this.setLocalGitStore);
        },
        clone : function(){
            this.error = null
            var g = this;
            this.$store.dispatch('gitstore/setUser', { 
                username: this.name,
                email: this.email 
            })
            this.$store.dispatch('gitstore/cloneRepo', {
                repo : this.repo_label,
                remote_url : this.remote_url
            }).then(function(err){
                if (err){
                    g.error = err
                }else{
                    g.repo = g.repo_label;
                    g.newR = false;
                }
            });
            this.$nextTick(this.setLocalGitStore);
        },
        pull : function(){
            this.$store.dispatch('gitstore/pullRepo', {
                repo : this.repo,
                remote_url : this.remote_url
            }).then(function(err){
                if (err){
                    g.error = err
                }
            });
        },
    },
    watch : {
        'menu' : function(newV){
            if (newV === true){
                this.reset();
            }
            if (this.repo == null){
                this.newR = true;
            }
        },
        'repo' : function(newV){
            this.load();
        },
        'name' : function(newV){
            this.$store.dispatch('gitstore/setUser', { 
                email: this.email,
                name: newV
            });
            this.setLocalGitStore();
        },
        'email' : function(newV){
            this.$store.dispatch('gitstore/setUser', { 
                email: newV,
                name: this.name 
            });
            this.setLocalGitStore();
        },
    }
}
</script>

<style>
	body .v-menu__content .v-card__title h4{
		margin-top: 0;
	}
    .placeholder{
        height: 40px;
        margin-bottom: 16px;
    }
    .v-card__text.giterror{
        font-size: 1.1em;
        line-height: 1.3em;
    }
</style>