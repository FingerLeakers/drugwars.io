<template>
    <div>
        <GangsTabs/>
        <Paginate class="ml-6 mt-4 text-center width-full" :page-count="Math.ceil(sent/25)" :page-range="3" :margin-pages="2" :click-handler="load_gangfights" :prev-text="'Prev'" :next-text="'Next'" :container-class="'pagination'" :page-class="'fight'"></Paginate>
        <div class="p-4">
            <div class="fight" v-for="fight in gangfights" :key="fight.fight_key || fight.transport_key">
                <ActionsFight v-if="fight.type === 'fight'" :fight="fight" />
                <ActionsTransport v-if="fight.type === 'transport'" :fight="fight" />
            </div>
            <p v-if="!gangfights || !gangfights.length">
                <Loading/>
            </p>
        </div>
        <Paginate class="ml-6 mb-4 mt-0 text-center width-full" :page-count="Math.ceil(sent/25)" :page-range="3" :margin-pages="2" :click-handler="load_gangfights" :prev-text="'Prev'" :next-text="'Next'" :container-class="'pagination'" :page-class="'fight'"></Paginate>
    </div>
</template>

<script>
import { mapActions } from 'vuex';
import Paginate from 'vuejs-paginate';
import { orderBy } from 'lodash';

export default {
  data() {
    return {
      id: this.$route.params.id,
      isInit: false,
      isLoading: false,
      gangs: null,
      user: this.$store.state.game.user.user,
    };
  },
  components: {
    Paginate,
  },
  created() {
    this.load_gangfights(1);
  },
  computed: {
    gangfights() {
      return orderBy(this.$store.state.game.sent_gangfights, 'end_date', 'desc');
    },
    sent() {
      if (this.$store.state.game.sent_gangfights)
        return this.$store.state.game.sent_gangfights.length;
      return 0;
    },
  },
  methods: {
    ...mapActions(['init', 'notify', 'refresh_sent_gangfights', 'force_sent_gangfights_refresh']),
    load_gangfights(start) {
      let end = 25;
      const id = this.id;
      end = start * 25;
      start = end - 25; // eslint-disable-line no-param-reassign
      if (start === 0) {
        this.force_sent_gangfights_refresh(true);
      } else {
        this.force_sent_gangfights_refresh(false);
      }
      this.refresh_sent_gangfights({ id, start, end })
        .then(() => {
          this.isLoading = false;
        })
        .catch(e => {
          console.error('Failed', e);
          this.isLoading = false;
        });
    },
  },
};
</script>

<style lang="less">
.pagination {
  margin-left: auto;
  margin-right: auto;
  display: -webkit-inline-box;
  list-style: none;
  a,
  span,
  em {
    background-color: #8080803b !important;
  }
}

li .disabled {
  background-color: #8080803b !important;
  color: #000000 !important;
  .pagination a {
    color: #000000 !important;
  }
}

.pagination a,
.pagination span,
.pagination em {
  background-color: #8080803b !important;
  color: #fbbd08;
  background: #1c1c1c !important;
  border: 1px solid #3e3e3e;
}

.pagination .gap,
.pagination .disabled,
.pagination .gap:hover,
.pagination .disabled:hover {
  color: #d1d5da;
  cursor: default;
  background-color: #8080803b !important;
}

.pagination a:hover,
.pagination a:focus,
.pagination span:hover,
.pagination span:focus,
.pagination em:hover,
.pagination em:focus {
  border: 1px solid #fbbd08;
}
</style>
