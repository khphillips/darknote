<template>
    <v-menu v-model="menu" :close-on-content-click="false" :nudge-width="200" offset-x>
        <template v-slot:activator="{ on }">
            <v-badge color="accent">
                <template v-slot:badge>
                    <span>!</span>
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
    created: function() {

    },
    computed: {
        username: {
            get() {
                return this.$store.state.gitstore ? this.$store.state.gitstore.username : ''
            },
            set(value) {
                this.$store.dispatch('gitstore/setUser', { username: value })
            }
        },
        email: {
            get() {
                return this.$store.state.gitstore ? this.$store.state.gitstore.email : ''
            },
            set(value) {
                this.$store.dispatch('gitstore/setUser', { email: value })
            }
        },
        repo: {
            get() {
                return this.$store.state.gitstore ? this.$store.state.gitstore.repo : ''
            },
            set(value) {
                this.$store.dispatch('gitstore/setRepo', value)
            }
        },
        remote_url: {
            get() {
                return this.$store.state.gitstore ? this.$store.state.gitstore.remote_url : ''
            },
            set(value) {
                this.$store.dispatch('gitstore/setRemoteUrl', value)
            }
        }
    },
    methods: {

    }
}
</script>

<style>
	body .v-menu__content .v-card__title h4{
		margin-top: 0;
	}
</style>