<template>
  <div id="app">
    <div id="drop_div" @drop="fileDropped" ref="dropDiv">
    </div>
    <div id="file_list" v-if="oldFileNames.length > 0">
      <div class="file" v-for="name in oldFileNames" :key="name">
        <span>{{ name }}</span><input type="text" spellcheck="false" v-model="newFileNames[name]">
      </div>
      <button id="check_button" @click="changeNames" />
    </div>
  </div>
</template>

<script>

export default {
  name: 'app',
  components: {

  },
  data() {
    return {
      oldFileNames: [],
      dirName: '',
      dirItem: null,
      newFileNames: {},
    };
  },
  mounted() {
    [
      'drag',
      'dragstart',
      'dragend',
      'dragover',
      'dragenter',
      'dragleave',
      'drop',
    ].forEach((evt) => {
      this.$refs.dropDiv.addEventListener(evt, (e) => {
        e.preventDefault();
        e.stopPropagation();
      });
    });

    [
      'dragover',
      'dragenter',
    ].forEach((evt) => {
      this.$refs.dropDiv.addEventListener(evt, () => {
        this.$refs.dropDiv.classList.add('over');
      });
    });

    [
      'dragend',
      'dragleave',
      'drop',
    ].forEach((evt) => {
      this.$refs.dropDiv.addEventListener(evt, () => {
        this.$refs.dropDiv.classList.remove('over');
      });
    });
  },
  methods: {
    fileDropped(e) {
      if (e.dataTransfer.items[0].webkitGetAsEntry().isDirectory) {
        // eslint-disable-next-line
        this.dirItem = e.dataTransfer.items[0];
        this.dirName = this.dirItem.getAsFile().name;
        this.oldFileNames = [];
        const directoryReader = this.dirItem.webkitGetAsEntry().createReader();
        directoryReader.readEntries((entries) => {
          entries.forEach((entry) => {
            this.oldFileNames.push(entry.name);
            this.newFileNames[entry.name] = entry.name;
          });
        });
      }
    },
    changeNames() {
      Object.keys(this.newFileNames).forEach((oldName) => {
        const newName = this.newFileNames[oldName];
        if (oldName !== newName) {
          console.log(`${oldName} WILL BE CHANGED TO ${newName}`);
        }
      });

      console.log(this.dataTransfer.files[0]);
    },
  },
};
</script>

<style lang="scss">
body {
  background-color: GHOSTWHITE;
  font-family: sans-serif;
  font-size: 12px;
  line-height: 18px;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

#drop_div {
  background-color: LIGHTSEAGREEN;
  width: 30vmin;
  height: 30vmin;
  margin: 48px auto;
  border-radius: 25%;
  display: flex;
  justify-content: center;
  align-items: center;
  box-shadow: 0 2px 10px rgba(SLATEGRAY, 0.3);

  &:before {
    content: '';
    background-image: url(./assets/folder-bg.svg);
    width: 50%;
    height: 50%;
  }

  &:after {
    content: "";
    position: absolute;
    width: 30vmin;
    height: 30vmin;
    margin: 0 auto;
    border-radius: 25%;
    transition: background-color .3s;
  }

  &.over:after {
    background-color: rgba(HONEYDEW, 0.3);
  }
}

#file_list {
  width: 50vmin;
  margin: 0 auto;
  display: flex;
  flex-direction: column;
  flex-wrap: nowrap;
  align-items: center;

  .file {
    display: flex;
    flex-wrap: nowrap;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 6px;
    box-sizing: border-box;
    padding: 6px;
    background-color: rgba(LIGHTSEAGREEN, 0.3);
    width: 100%;

    span {
      font-weight: 700;
    }

    input {
      padding: 6px;
      border: 0;
      font-size: 12px;
      line-height: 18px;
      flex-basis: 50%;
    }

    input:focus{
      outline: none;
      background-color: CORNSILK;
    }
  }

  button {
    height: 48px;
    width: 96px;
    margin-top: 24px;
    font-size: 24px;
    font-weight: 700;
    line-height: 48px;
    border: 0;
    background-color: LIGHTSEAGREEN;
    transition: all .3s;
  }

  button:after {
    content: "Check";
    color: HONEYDEW;
    position: absolute;
    height: 48px;
    width: 96px;
    transform: translate(-50%,-50%);
    transition: all .3s;
  }

  button:hover:after {
    background-color: rgba(HONEYDEW, 0.3);
  }

  button:hover {
    box-shadow: 0 2px 10px rgba(SLATEGRAY, 0.3);
  }

  button:focus{
    outline: none;
  }

  button:active:after {
    background-color: rgba(HONEYDEW, 0.6);
  }

  button:active {
    box-shadow: 0 0 5px rgba(SLATEGRAY, 0.3);
  }

}
</style>

<!-- const myNewFile = new File([myFile], 'new_name.png', {type: myFile.type}); -->
