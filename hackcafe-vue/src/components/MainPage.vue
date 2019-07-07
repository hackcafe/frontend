<template>
  <v-container fluid grid-list-xl>
    <v-layout row wrap>
      <v-flex
        v-for="hack in hacks.filter(hack => hack.category === currentCategory)"
        :key="hack.id"
        shrink>
        <v-card
          width='350px'
          min-height='120px'
          ripple
          elevation=10
          @click.native="cardClick(hack.id)"
          :color="!hack.enabled ? 'grey darken-2' : 'black'">
          <v-card-title primary-title>
            <div>
              <h3 class="headline mb-0">{{ hack.name }} <v-icon v-if="hack.enabled">check</v-icon></h3>

              <div>{{ hack.description }}</div>
            </div>
          </v-card-title>
        </v-card>
      </v-flex>

      <v-btn
        color="error"
        @click="showReceipt"
        fab right bottom
        fixed large dark
        :loading="loadingDialog">
        <v-icon>check</v-icon>
      </v-btn>

      <v-dialog
        v-model="loadingDialog"
        persistent
        width="400px">
        <v-card
          color="primary"
          dark>
          <v-card-text>
            Grab some coffee while we prepare your package...
            <v-progress-linear
              indeterminate
              color="white"
              class="mb-0"
            ></v-progress-linear>
          </v-card-text>
        </v-card>
      </v-dialog>

      <v-dialog
        v-model="receiptDialog"
        width="500px">
        <v-list two-line v-if="hacks.filter(hack => hack.enabled).length > 0">
          <h1 class="display font-weight-light px-4">Comfirm Hacks to Export</h1>
          <template v-for="(hack, index) in hacks.filter(hack => hack.enabled)">
            <v-divider :key="`top-divider-${index}`"></v-divider>

            <v-list-tile
              :key="hack.id"
              ripple>
              <v-list-tile-content>
                <v-list-tile-title v-html="hack.name"></v-list-tile-title>
                <v-list-tile-sub-title v-html="hack.description"></v-list-tile-sub-title>
              </v-list-tile-content>
            </v-list-tile>

            <v-divider :key="`bottom-divider-${index}`"></v-divider>
          </template>

          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="red" flat @click="receiptDialog = false">CANCEL</v-btn>
            <v-btn color="green" @click="sendTicket">CONFIRM</v-btn>
          </v-card-actions>
        </v-list>
        <v-card v-else>
          <v-card-title class="headline" primary-title>Alert</v-card-title>
          <v-card-text>You need to select some hacks first!</v-card-text>

          <v-divider></v-divider>

          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn
              color="blue"
              flat
              @click="receiptDialog = false">
              OK
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>

      <v-bottom-nav
        :active.sync="currentCategory"
        :value="true"
        absolute
        height=70
        color="cyan darken-2">

        <v-btn
          color="white" flat
          v-for="category in categories"
          :key="category.id"
          :value="category.id">
          <span class="font-weight-light">{{ category.name }}</span>
          <history-icon>history</history-icon>
        </v-btn>

      </v-bottom-nav>
    </v-layout>
  </v-container>
</template>

<script>
import HistoryIcon from 'vue-material-design-icons/History';

import axios from 'axios';

const HTTP = axios.create({
  baseURL: `http://536be0e0.ngrok.io/`,
  cors: true,
})

export default {
  components: {
    HistoryIcon
  },
  mounted() {
    HTTP
      .get('fetch-hacks')
      .then(res => {
        this.categories = res.data['hack-categories'];
        this.currentCategory = this.categories[0].id;
        this.hacks = res.data['all-hacks'];
        this.hacks = this.hacks.map(hack => {
          hack['enabled'] = false;
          return hack;
        });
      });
  },
  data: () => ({
    categories: [],
    currentCategory: '',
    hacks: [],
    loadingDialog: false,
    receiptDialog: false,
  }),
  methods: {
    showReceipt: function(event) {
      // if (this.selectedHacks.length > 0)
        this.receiptDialog = true;
    },
    sendTicket: function(event) {
      this.loadingDialog = true;
      this.receiptDialog = false;

      HTTP
        .post('request-package', this.selectedHacks)
        .then(res => {
          console.log(res.data);
          setTimeout(() => {
            this.loadingDialog = false;
          }, 4000);
        });
    },
    cardClick: function(hackId) {
      for (const i in this.hacks) {
        if (this.hacks[i].id === hackId) {
          this.hacks[i].enabled = !this.hacks[i].enabled;
        }
      }
      this.$forceUpdate();
    }
  },
  computed: {
    selectedHacks: function() {
      return this.hacks
        .filter(hack => hack.enabled)
        .map(hack => hack.id);
    }
  },
  watch: {
    dialog (val) {
      if (!val) return;

      setTimeout(() => (this.dialog = false), 4000);
    }
  }
}
</script>

<style>
.material-design-icon.icon-2x {
  height: 2em;
  width: 2em;
}
.material-design-icon.icon-2x > .material-design-icon__svg {
  height: 2em;
  width: 2em;
}
</style>