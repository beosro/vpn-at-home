<template>
    <nav class="ui top fixed menu">
        <div class="ui container">
            <router-link active-class="active" class="item" to="/" exact>Home</router-link>
            <router-link v-if="isSuperuser" active-class="active" class="item" to="/settings" exact>Settings</router-link>
            <a v-if="isSuperuser" class="item" href="/admin/">Admin</a>
            <div class="right menu">
                <div v-if="canAddClient" class="ui item">
                    <div class="ui primary button compact add-button" @click="onClickedAddClient">Add client</div>
                </div>
                <div v-if="canAddServer" class="ui item">
                    <div class="ui primary button compact" @click="onClickedAddServer">Add server</div>
                </div>
                <div class="ui item">
                    <span :class="{'superuser': isSuperuser}"><b>{{email}}</b></span>
                </div>
                <div class="ui item no-padding-right">
                    <div class="ui primary button compact negative" @click="onClickedLogout">Logout</div>
                </div>
            </div>
        </div>
    </nav>
</template>

<script>
import { Component, Vue } from 'vue-property-decorator';
import { ROUTE_HOME } from '@/router';
import {
    EVENT_CLICKED_ADD_CLIENT,
    EVENT_CLICKED_ADD_SERVER
} from '@/eventbus';

@Component({
    name: 'NavigationBar',
    components: {
    }
})
export default class NavigationBar extends Vue {

    onClickedLogout () {
        this.$api.logout(this.onLogoutCompleted.bind(this));
    }

    onClickedAddClient () {
        this.$root.$emit(EVENT_CLICKED_ADD_CLIENT);
    }

    onClickedAddServer () {
        this.$root.$emit(EVENT_CLICKED_ADD_SERVER);
    }

    onLogoutCompleted () {
        this.$store.commit('logout');
        this.$router.push({ path: '/login' });
    }

    get canAddServer () {
        return this.$route.name === ROUTE_HOME;
    }

    get canAddClient () {
        return this.$route.name === ROUTE_HOME && this.$store.getters.hasServer;
    }

    get isSuperuser () {
        return this.$store.getters.isSuperuser;
    }

    get email () {
        return this.$store.state.user.email;
    }

}
</script>

<style lang="scss" scoped>
@import "../assets/main.scss";

.superuser {
    color: $red;
}

.no-padding-right {
    padding-right: 0 !important;
}

</style>
