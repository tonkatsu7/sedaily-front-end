
<template>
  <div>
    <modal v-if="showModal">
      <span slot="header">Sign up for a personalized feed</span>
      <span slot="body">
        <router-link to="/login">Log in</router-link> or <router-link to="/register">register</router-link>.
      </span>
      <button slot="footer" class="modal-default-button btn btn-secondary" @click="closeModal">
        I'll do it later
      </button>
    </modal>

    <h1> Feed </h1>

    <div class="feed-list">
      <feed-item v-for="feedItem in feed" :key="feedItem._id" :feedItem="feedItem" />
    </div>
  </div>
</template>

<script>
import FeedItem from '@/components/FeedItem.vue'
import Modal from '@/components/Modal.vue'
import { mapActions, mapState, mapGetters } from 'vuex'
export default {
  name: 'feed-view',

  components: {
    FeedItem,
    Modal
  },

  data () {
    return {
      loading: true,
      showModal: false
    }
  },

  beforeMount () {
    this.showModal = !this.isLoggedIn
    // You probably only need finally instead of both then and catch
    this.fetchMyProfileData()
      .then(() => {
        console.log('fetch my feed?------')
        return this.fetchMyFeed({ userId: this.me._id })
          .then((feedItems) => {
            this.loading = false
          })
      })
      .catch((error) => {
        console.log('error logging in', error)
        this.fetchMyFeed({ userId: null })
          .then((feedItems) => {
            this.loading = false
          })
      })
  },
  methods: {
    closeModal: function () {
      this.showModal = false
    },
    ...mapActions(['fetchMyProfileData', 'fetchMyFeed'])
  },

  computed: {
    ...mapGetters(['isLoggedIn']),
    ...mapState({
      me (state) {
        return state.me
      },
      feed (state) {
        return state.feed
      }
    })
  }
}
</script>


<style scoped lang="stylus">
h1
  text-align center
  margin 30px auto

.feed-list
  display flex
  flex-direction column
  justify-content space-around
</style>
