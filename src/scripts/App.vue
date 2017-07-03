<template>
  <div id="app" class="popup-content">
    <div v-if="token === null">
      <md-input-container>
        <label>Add Firebase App Id</label>
        <md-input v-model="firebaseId"></md-input>
      </md-input-container>
      <md-button class="md-primary" style="float:right" @click="addAppId(firebaseId)">Add</md-button>
    </div>
    <div v-if="token !== null">
      <p>
        <img :src="bookmark.favicon" width="15" height="15"> {{ domain }}
      </p>
      <p>
        <span class="md-title"> {{ bookmark.title }}</span>
      </p>
      <p>
        <span class="md-body-1">{{ bookmark.description }}</span>
      </p>
      <md-input-container>
        <label for="category">Choose Category</label>
        <md-select name="category" id="category" v-model="category">
          <md-option :value="category.name" v-for="category in categories" :key="category.name">{{ category.name }}</md-option>
        </md-select>
      </md-input-container>
      <md-button class="md-primary" style="float:right" @click="addBookmark(bookmark)">Add Bookmark</md-button>
    </div>
  </div>
</template>
<script>
import ext from "./utils/ext";
import storage from "./utils/storage";
import firebase from 'firebase';
import _ from 'lodash';
import URI from 'urijs'

export default {
  data() {
    return {
      firebaseId: null,
      token: null,
      category: null,
      categories: [],
      db: null,
      domain: null,
      bookmark: {
        title: null,
        description: null,
        url: null,
        favicon: null,
        category: {
          name: null,
          color: null
        }
      }
    }
  },
  beforeCreate() {
    const self = this;
    ext.tabs.query({ active: true, currentWindow: true }, function (tabs) {
      var activeTab = tabs[0];
      chrome.tabs.sendMessage(activeTab.id, { action: 'process-page' }, (data) => {
        self.bookmark.title = data.title;
        self.bookmark.description = data.description
        self.bookmark.favicon = activeTab.favIconUrl
        self.bookmark.url = data.url
        self.domain = new URI(data.url).domain()
      });
    });
  },
  created() {
    const self = this;
    storage.get('id', function (res) {
      if (res.id) {
        self.token = res.id;
        const databaseUrl = `${self.token}.firebaseio.com`;
        const fire = firebase.initializeApp({
          databaseURL: databaseUrl
        });
        self.db = fire.database();
        const categories = self.db.ref('categories');
        const bookmarks = self.db.ref('bookmarks');
        categories.on('value', (snapshot) => {
          self.categories = snapshot.val();
        }, function (err) {
          console.log(err.code);
        });
      }
    });
  },
  methods: {
    addBookmark(data) {
      const category = _.filter(this.categories, ['name', this.category]);
      data.category = category[0];
      this.db.ref('bookmarks').push(data);
      window.close();
    },
    addAppId(id) {
      storage.set({ 'id': id }, function () {
        console.log('Firebase Id used');
      });
      this.token = id
    }
  }
}
</script>