

<template>
    <header>
        <h1>Hey Mark!</h1>
    </header>
    <nav>
        <ApolloMutation
        :mutation="mutation"
        :variables="{
            username,
            password
        }"
        @done="cleanup"
        >
        <template slot-scope="{mutate, error}">
        <form @submit.prevent="submitLogin">
            <label for="username">Username:</label>
            <input type="text" id="username" v-model="username" placeholder="username" />
            <label for="password">Password:</label>
            <input type="text" id="password" v-model="password" placeholder="password" />
            <input type="submit" value="Log in" />
        </form>
        </template>
        </ApolloMutation>
    </nav>
    <div id="timeline">
        <div v-if=appState.timeline>
            <div v-for="bookmark of appState.timeline" :key="bookmark.id">
                <div><a v-bind:href="'http://localhost:9456/bookmark/' + bookmark.id">{{ bookmark.url }}</a></div>
                <div>{{ bookmark.description }}</div>
                <div>{{ bookmark.owner.username }}</div>
            </div>
        </div>
    </div>
</template>
<script lang="ts">
import { inject, ref, watch } from 'vue';
import { useQuery, provideApolloClient, DefaultApolloClient } from '@vue/apollo-composable';
import gql from 'graphql-tag';

export default {
    appState: {
        user: {},
        timeline: [],
    },

    setup() {
        const appState = ref({
            user: {},
            timeline: [],
        })

        return { appState }
    },

    data() {
        return {
            user: {},
            timeline: [],
            mutation: gql`
            mutation authenticate($username: String!, $password: String!) {
                authenticate(username: $username, password: $password) @client
            }
            `
        }
    },

    mounted() {
        const { result } = useQuery(gql`
    query getTimeline {
        timeline {
            id
            url
            description
            owner {
                id
                username
            }
            visibility
        }
    }
`)
        watch(result, value => {
            console.log(this.appState)
            this.appState.timeline = value.timeline
            console.log(this.appState)
        })
    },

    methods: {
        cleanup() {
            this.username = ""
            this.password = ""
        }
    }

    
}

/*

const client = inject(DefaultApolloClient);
const { result } = client.query(gql`
    query getTimeline {
        timeline {
            id
            url
            description
            owner {
                id
                username
            }
            visibility
        }
    }
`)

const username = ref("")
const password = ref("")
function submitLogin() {
    const { result } = useQuery(gql`
    query Authenticate {
        authenticate {
            id
        }
    }
    `)

    watch(result, value => {
        console.log(value)
    })
}

*/
const result = ref({})
</script>