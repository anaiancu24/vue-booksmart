<template>
  <div id="booksmart">
    <div class="booksmart__logo">
      <img src="../assets/bookmark_without_border.png" />
      <h1>Booksmart</h1>
    </div>
    <div class="booksmart__add-folder">
      <h2>Add a folder</h2>
      <input
        type="text"
        id="booksmart__add-folder-input"
        placeholder="Type here..."
        v-model="newFolder"
        v-on:keyup.enter="addFolder(newFolder)"
      />
      <button
        class="booksmart__add-folder-button"
        type="submit"
        id="addFolderButton"
        @click="addFolder(newFolder)"
      >
        +
      </button>
    </div>
    <div v-if="!loading">
      <div
        id="booksmart__folders-list"
        class="booksmart__folders-list"
        v-for="(folder, index) in folders"
        :key="index"
      >
        <div
          v-if="deleteConfirmation[index][index]"
          class="booksmart_delete-confirmation"
        >
          <h2>You sure about that?</h2>
          <p>All your bookmarks within this folder will be deleted, too.</p>
          <div class="buttons">
            <button @click="deleteFolder(index)">Delete</button>
            <button @click="deleteConfirmation[index][index] = false">
              Cancel
            </button>
          </div>
        </div>
        <div class="booksmart__folder-element">
          <div
            class="booksmart__folder-label"
            v-bind:style="{ backgroundColor: colors[index] }"
            @click="togglePages(index)"
          >
            <button
              v-if="
                Object.values(folder)[0].length > 0 && !openPages[index][index]
              "
              class="booksmart__dropdown-button booksmart__dropdown-button-open"
            ></button>
            <button
              v-if="
                Object.values(folder)[0].length > 0 && openPages[index][index]
              "
              class="booksmart__dropdown-button booksmart__dropdown-button-hide"
            ></button>
            <h3>
              {{ Object.keys(folder)[0] }}
            </h3>
          </div>

          <button
            class="booksmart__remove-button"
            @click="deleteConfirmation[index][index] = true"
          >
            -</button
          ><button
            class="booksmart__add-button"
            @click="addPage(index, Object.keys(folder)[0])"
          >
            +
          </button>
        </div>
        <ul
          class="booksmart__links-list"
          v-if="Object.values(folder)[0].length > 0 && openPages[index][index]"
        >
          <li
            class="booksmart__link-item"
            v-for="(page, subindex) in Object.values(folder)[0]"
            :key="subindex"
          >
            <a :href="page.url" target="_blank">{{ page.title }}</a
            ><button
              class="booksmart__remove-button"
              @click="deletePage(index, subindex)"
            >
              -
            </button>
          </li>
        </ul>
      </div>
    </div>
  <a href="https://www.paypal.com/donate?hosted_button_id=ZGVSUWAXLH2SY" target="_blank"> 
  <div class="booksmart__donate-label">
    <img src="../assets/Coffee_Icon.svg" />
    <span>Buy me a coffee</span>
    </div>
  </a>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      newFolder: null,
      folders: [],
      pages: [],
      loading: true,
      openPages: [
        { 0: false },
        { 1: false },
        { 2: false },
        { 3: false },
        { 4: false },
        { 5: false },
        { 6: false },
        { 7: false },
        { 8: false },
        { 9: false },
        { 10: false },
        { 11: false },
        { 12: false },
        { 13: false },
        { 14: false },
        { 15: false },
        { 16: false },
        { 17: false },
        { 18: false },
        { 19: false },
        { 20: false },
        { 21: false },
        { 22: false },
        { 23: false },
        { 24: false },
        { 25: false },
        { 26: false },
        { 27: false },
        { 28: false },
        { 29: false },
        { 30: false }
      ],
      colors: [
        "#fda6a5",
        "#b95072",
        "#893c55",
        "#d66175",
        "#d62155",
        "#e66165",
        "#b95a7a",
        "#eda4b5",
        "#a83a55",
        "#9c335a",
        "#fda6a5",
        "#b95072",
        "#893c55",
        "#d66175",
        "#d62155",
        "#e66165",
        "#b95a7a",
        "#eda4b5",
        "#a83a55",
        "#9c335a",
        "#fda6a5",
        "#b95072",
        "#893c55",
        "#d66175",
        "#d62155",
        "#e66165",
        "#b95a7a",
        "#eda4b5",
        "#a83a55",
        "#9c335a",
      ],
      deleteConfirmation: [
        { 0: false },
        { 1: false },
        { 2: false },
        { 3: false },
        { 4: false },
        { 5: false },
        { 6: false },
        { 7: false },
        { 8: false },
        { 9: false },
        { 10: false },
        { 11: false },
        { 12: false },
        { 13: false },
        { 14: false },
        { 15: false },
        { 16: false },
        { 17: false },
        { 18: false },
        { 19: false },
        { 20: false },
        { 21: false },
        { 22: false },
        { 23: false },
        { 24: false },
        { 25: false },
        { 26: false },
        { 27: false },
        { 28: false },
        { 29: false },
        { 30: false }
      ],
    };
  },
  mounted() {
    this.loadFolders();
  },
  methods: {
    // donateLink() {
    //   console.log('donate')
    //   window.location = "https://www.paypal.com/donate?hosted_button_id=ZGVSUWAXLH2SY"
    // },
    togglePages(index) {
      this.openPages[index][index] = !this.openPages[index][index];
    },
    deletePage(index, subindex) {
      Object.values(this.folders[index])[0].splice(subindex, 1);
      chrome.storage.local.set({ folderList: this.folders });
    },
    addPage(index, key) {
      chrome.tabs.query({ active: true, currentWindow: true }, (tabs) => {
        let pageObject = {};
        pageObject["title"] = tabs[0].title;
        pageObject["url"] = tabs[0].url;
        this.folders[index][key].push(pageObject);
        chrome.storage.local.set({ folderList: this.folders });
      });
      this.openPages[index][index] = true;
    },
    deleteFolder(index) {
      this.folders.splice(index, 1);
      chrome.storage.local.set({ folderList: this.folders });
      this.deleteConfirmation[index][index] = false;
    },
    loadFolders() {
      chrome.storage.local.get("folderList", (data) => {
        // check if there are any folders already
        if (data.folderList === undefined || data.folderList.length == 0) {
          chrome.storage.local.set({ folderList: [] });
        } else {
          this.folders = data.folderList;
          this.loading = false;
        }
      });
    },
    addFolder(newFolder) {
      let newFolderObject = {};
      newFolderObject[newFolder] = [];
      this.folders.push(newFolderObject);
      chrome.storage.local.set({ folderList: this.folders });
      this.newFolder = null;
      this.loading = false;
    },
  },
};
</script>

<style>

@import url('https://fonts.googleapis.com/css2?family=Caveat&family=Roboto:wght@300;400&display=swap');

body {
  margin: 0;
}

#booksmart {
  background-image: linear-gradient(137deg, #423759 -85%, #19192d 100%);
  width: 520px;
  padding: 24px 65px;
  font-family: 'Roboto', sans-serif;
  min-height:400px;
}

.booksmart__logo {
  display: flex;
  align-items: center;
  margin-bottom: 20px;
}

.booksmart__logo img {
  height: 25px;
  margin-right: 5px;
}
.booksmart__logo h1 {
  font-family: "Ubuntu", sans-serif;
  font-size: 18px;
  color: #d2d2d2;
  font-weight: 500;
}

.booksmart__add-folder {
  position: relative;
  width: 262px;
  height: 53px;
  border-radius: 3px;
  box-shadow: 0 4px 8px 1px rgba(31, 30, 30, 0.5);
  background-image: linear-gradient(169deg, #543f95 2%, #d66175 79%);
  /* background: url("../assets/bookmark_add_folder_bg.png") no-repeat;
  background-size: contain; */
  margin: 0 auto 20px;
  padding: 14px 43px;
}

.booksmart__add-folder h2 {
  font-size: 12px;
  color: white;
  margin-bottom: 7px;
  margin-top: 0;
}
 /* .booksmart__input-folder-wrapper {
   display:flex;
   justify-content: center;
   align-items: center;
 } */

.booksmart__add-folder input {
  border-radius: 3px;
  background-color: rgba(26, 25, 25, 0.66);  
  width: 249px;
  height: 30px;
  border: none;
  padding-left: 10px;
  color: white;
  font-family: 'Roboto', sans-serif;
  outline: none;
  /* margin-right: 12px; */
}

.booksmart__add-folder input::placeholder {
  color: #cac8c8;
  opacity: 0.3;
  font-size: 12px;
  font-family: "Lato", sans-serif !important;
}

.booksmart__add-folder-button {
  width: 32px;
  height: 26px;
  background-color: #1bbc98;
  border-radius: 3px;
  border: none;
  color: white;
  font-size: 18px;
  outline: none;
  cursor: pointer;
  position: absolute;
    top: 38px;
    right: 47px;
}

.booksmart__folders-list {
  list-style-type: none;
  text-align: center;
  margin-bottom:20px;
}

.booksmart__folder-element {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-bottom: 10px;
}

.booksmart__folder-label {
  position:relative;
  padding:4px;
  width: 145px;
  height: 26px;
  border-radius: 3px;
  box-shadow: 0 4px 4px 0 rgba(0, 0, 0, 0.2);
  background-color: #b95072;
  text-align: center;
  display: inline-block;
  margin-right: 10px;
  position: relative;
  cursor: pointer;
  display: flex;
  justify-content: center;
  align-items: center;
}

.booksmart__folder-label h3 {
  font-size: 14px;
    color: white;
    font-weight: 400;
}

.booksmart__dropdown-button {
  background: url("../assets/chevron.svg") 3px 5px no-repeat !important;
  position: absolute;
  left: 10px;
  top: 11px;
  width: 10px;
  height: 10px;
  color: white;
  border: none;
  outline: none;
  background: none;
  cursor: pointer;
  transition: all 0.2s ease;
}

.booksmart__dropdown-button-open {
  transform: rotate(0deg);
}
.booksmart__dropdown-button-hide {
  transform: rotate(-180deg);
  left: 13px;
  top: 15px;
}


.booksmart__folder-element button {
  display:flex;
  justify-content: center;
  align-items: center;
}

.booksmart__remove-button {
  width: 20px;
  height: 20px;
  background-color: #d55962;
  color: white;
  border: none;
  outline: none;
  cursor: pointer;
  text-align: center;
  border-radius: 50%;
  margin-right: 5px;
  font-size: 16px;
}

.booksmart__add-button {
  width: 20px;
  height: 20px;
  background-color: #1bbc98;
  color: white;
  border: none;
  outline: none;
  cursor: pointer;
  border-radius: 50%;
  font-size: 16px;
}

.booksmart__links-list {
  display: block;
  padding: 12px 20px;
  border-radius: 3px;
  background: white;
}

.hidden {
  display: none;
}

.booksmart__link-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 15px;
}

.booksmart__link-item:last-child {
  margin-bottom: 0;
}

.booksmart__link-item button {
  width: 15px;
  height: 15px;
  font-size: 10px;
}

.booksmart__link-item a {
  color: #928d8d;
  text-decoration: none;
  text-align: left;
}

.booksmart__link-item a:hover {
  color: #d55962;
}

.booksmart__link-item li {
  list-style-type: none;
}

.booksmart_delete-confirmation {
  position: relative;
  z-index: 100;
  width: 200px;
  margin: 0 auto;
  left: 0;
  right: 0;
  background: white;
  border-radius: 3px;
  box-shadow: 0 4px 13px 1000px rgb(4 4 4 / 50%);
  text-align: center;
  padding: 16px;
}

.booksmart_delete-confirmation h2 {
  margin: 0;
  color: #131313;
  font-size:16px;
  margin-bottom:10px;
}

.booksmart_delete-confirmation p {
  color: #585757;
  font-size:12px;
  margin-bottom:10px;
}

.booksmart_delete-confirmation button {
  background: white;
  color: #585757;
  font-size:14px;
  border-radius: 3px;
  box-shadow: 0 1px 2px 0 rgba(140, 138, 138, 0.5);
  border-style: solid;
  border-width: 2px;
  border-image-source: linear-gradient(171deg, #543f95 -4%, #d66175 83%);
  border-image-slice: 1;
  background-image: linear-gradient(to bottom, var(--fill-white-bg), var(--fill-white-bg)), linear-gradient(171deg, #543f95 -4%, #d66175 83%);
  background-origin: border-box;
  background-clip: content-box, border-box;
  padding: 4px 12px;
  outline: none;
  cursor: pointer;
}

.booksmart_delete-confirmation .buttons {
  display: flex;
  justify-content: space-around;
}

.booksmart__donate-label {
  position:absolute;
  bottom:10px;
  right:-43px;
  background:white;
  padding:5px 10px;
  border-top-left-radius:10px;
  border-bottom-left-radius:10px;
  width: 55px;
  display: flex;
  justify-content: center;
  align-items: center;
  text-decoration: none;
  transition: all .3s ease;
  cursor:pointer;
}

.booksmart__donate-label:hover {
  right:0;
}

.booksmart__donate-label img {
  margin-right:5px;
  height:25px;
}

.booksmart__donate-label span {
  font-size:11px;
  color:#2e2c2c;
  font-family: 'Caveat', cursive;
  line-height: 1;
}
</style>
