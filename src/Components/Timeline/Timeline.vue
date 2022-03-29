<script setup lang="ts">
import { reactive, watch, inject, ref } from 'vue';
import { DefaultApolloClient, useQuery } from '@vue/apollo-composable';
import gql from 'graphql-tag';
import type { ApolloClient, NormalizedCacheObject } from '@apollo/client/core';

const client: ApolloClient<NormalizedCacheObject> | null | undefined = inject(DefaultApolloClient)

const state = reactive({
    timeline: [
        {"id": 1, "owner": {username: "test"}, "url": "https://google.com", "description": "Evil"},
        {"id": 2, "owner": {username: "test2"}, "url": "https://duckduckgo.com", "description": "Not So Evil"},
    ]
});

function mounted() {
    refreshTimeline();
}

function refreshTimeline() {
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
        state.timeline = value.timeline
    })
}

function refreshTimeline2() {
    if ( null == client || undefined == client ) {
        return
    }

    const p = client.watchQuery({
        query: gql`query getTimeline {
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
    }`,
    fetchPolicy: 'no-cache',
    pollInterval: 1000
    })
    
    const x = ref({})
    watch(x, value => {
        state.timeline = value.timeline;
    })

    p.result().then(function (r) {
        // state.timeline = r.data.timeline
        console.log(r.data)
        x.value = r.data
    }).catch(function(reason) {
        console.log(reason)
    })
}
</script>

<template>
    <button @click="refreshTimeline2">Refresh</button>
    <div v-if="state && state.timeline">
        <div v-for="link in state.timeline" :key="link.id">
            {{ link.owner.username }}: <a :href="link.url" target="_blank">{{ link.description }}</a>
        </div>
    </div>
</template>

