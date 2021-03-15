<template>
    <div>
        <h1>Channel Point Redemptions:</h1>
        <a v-if="!listening" href="#" @click="startListening()">Start Listening</a>
        <a v-if="listening" href="#" @click="stopListening()">Stop Listening</a>
        <div v-for="(redemption, index) in redemptions" :key="index">
            {{ redemption }}
        </div>
    </div>
</template>
<script>
import { ApiClient } from 'twitch'
import { StaticAuthProvider } from 'twitch-auth'
import { PubSubClient, PubSubRedemptionMessage } from 'twitch-pubsub-client'

export default {
    props: ['access_token'],
    data: function () {
        return {
            redemptions: [],
            listening: false,
            listener: false,
            twitchApiClient: false,
            twitchPubSubClient: false,
            userId: false
        }
    },
    methods: {
        getApiClient: async function () {
            if(this.access_token){
                const clientId = process.env.VUE_APP_TWITCH_CLIENT_ID
                const accessToken = this.access_token
                const authProvider = new StaticAuthProvider(clientId, accessToken)
                this.twitchApiClient = new ApiClient({ authProvider })
                this.twitchPubSubClient = new PubSubClient()
                this.userId = await this.twitchPubSubClient.registerUserListener(this.twitchApiClient)

            }
        },
        startListening: async function () {
            if(!this.twitchApiClient) {
                await this.getApiClient()
            }

            this.listener = await this.twitchPubSubClient.onRedemption(this.userId, (message) => {
                let redemption = `${message.userDisplayName} just redeemed ${message.rewardTitle}`
                console.log(redemption)
                this.redemptions.push(redemption)

                if(this.redemptions.length > 5) this.redemptions.pop()
            })
            
            this.listening = true
        },
        stopListening: function () {
            this.listener.remove()

            this.listening = false
        }
    }
}
</script>
