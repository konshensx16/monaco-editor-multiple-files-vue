<template>
  <div style="display: flex; height: 100vh; width: 100vw">
    <div id="files" style="width: 240px;border-right: 1px solid rgba(0, 0, 0, 0.08);">
      <template v-for="(file, index, key) in files">
        <div @click.prevent="current = index" :class="{'active': current == index}">{{ index }} -
          <a style="color: red" @click="editFileName(index)"><small><em>Edit name</em></small></a></div>
      </template>
        <input type="text" class="input" v-model="newFileName" @keyup.enter="newFile"
        placeholder="File name here (press enter to validate)">
    </div>
    <div style="display: flex;flex: 1 1 0%;position: relative;overflow: hidden;">
      <Edito
        @codeChanged="codeChanged"
        :files="files"
        :path="current"
        :value="files[current]"
      />
    </div>
  </div>
</template>

<script>
  import Vue from 'vue';
  import Edito from "./Edito";
  // IMPORTANT NOTE: it seems like the state of the editor when changing between files only saves when the files don't
  //  errors, i checked multiple times and itseems like that is the case, creating a regular file that contains rubish
  // does not work with the following code
  export default {
    name: 'files',
    components: {Edito},
    data: () => ({
      newFileName: '',
      current: 'Main.js',
      files: {
        'App.js': `import React, { Component } from 'react';
            import { Text, View, StyleSheet } from 'react-native';
            import { Constants } from 'expo';
            import AssetExample from './AssetExample';
            export default class App extends Component {
              render() {
                return (
                  <View style={styles.container}>
                    <Text style={styles.paragraph}>
                      Change code in the editor and watch it change on your phone!
                      Save to get a shareable url.
                    </Text>
                    <AssetExample />
                  </View>
                );
              }
            }
            const styles = StyleSheet.create({
              container: {
                flex: 1,
                alignItems: 'center',
                justifyContent: 'center',
                paddingTop: Constants.statusBarHeight,
                backgroundColor: '#ecf0f1',
              },
              paragraph: {
                margin: 24,
                fontSize: 18,
                fontWeight: 'bold',
                textAlign: 'center',
                color: '#34495e',
              },
            });`,
        'AssetExample.js': `import React, { Component } from 'react';
            import { Text, View, StyleSheet, Image } from 'react-native';
            export default class AssetExample extends Component {
              render() {
                return (
                  <View style={styles.container}>
                    <Text style={styles.paragraph}>
                      Local files and assets can be imported by dragging and dropping them into the editor
                    </Text>
                    <Image style={styles.logo} source={require("../assets/expo.symbol.white.png")}/>
                  </View>
                );
              }
            }
            const styles = StyleSheet.create({
              container: {
                alignItems: 'center',
                justifyContent: 'center',
              },
              paragraph: {
                margin: 24,
                marginTop: 0,
                fontSize: 14,
                fontWeight: 'bold',
                textAlign: 'center',
                color: '#34495e',
              },
              logo: {
                backgroundColor: "#056ecf",
                height: 128,
                width: 128,
              }
            });`,
        'Main.js': `import React, { Component } from 'Main.js_file_';
            import { Text, View, StyleSheet } from 'react-native';
            import { Constants } from 'expo';
            import AssetExample from './AssetExample';
            export default class App extends Component {
              render() {
                return (
                  <View style={styles.container}>
                    <Text style={styles.paragraph}>
                      Change code in the editor and watch it change on your phone!
                      Save to get a shareable url.
                    </Text>
                    <AssetExample />
                  </View>
                );
              }
            }
            const styles = StyleSheet.create({
              container: {
                flex: 1,
                alignItems: 'center',
                justifyContent: 'center',
                paddingTop: Constants.statusBarHeight,
                backgroundColor: '#ecf0f1',
              },
              paragraph: {
                margin: 24,
                fontSize: 18,
                fontWeight: 'bold',
                textAlign: 'center',
                color: '#34495e',
              },
            });`,
        'Example.js': `import React, { Component } from 'react';
            import { Text, View, StyleSheet, Image } from 'react-native';
            export default class AssetExample extends Component {
              render() {
                return (
                  <View style={styles.container}>
                    <Text style={styles.paragraph}>
                      Local files and assets can be imported by dragging and dropping them into the editor
                    </Text>
                    <Image style={styles.logo} source={require("../assets/expo.symbol.white.png")}/>
                  </View>
                );
              }
            }
            const styles = StyleSheet.create({
              container: {
                alignItems: 'center',
                justifyContent: 'center',
              },
              paragraph: {
                margin: 24,
                marginTop: 0,
                fontSize: 14,
                fontWeight: 'bold',
                textAlign: 'center',
                color: '#34495e',
              },
              logo: {
                backgroundColor: "#056ecf",
                height: 128,
                width: 128,
              }
            });`,
      },
    }),
    computed: {},
    methods: {
      codeChanged: function (code) {
        this.files = {
          ...this.files,
          [this.current]: code
        }
      },
      newFile() {
        this.files = {
          ...this.files,
          [this.newFileName]: '// Write code here'
        }

        this.newFileName = '';
      },
      editFileName: function (path) {
        const input = prompt('Enter the new file name');
        if (input != null) {
          this.files = {
            ...this.files,
            [input]: this.files[path]
          };
          Vue.delete(this.files, path);
          this.$emit('renameFile', {
            oldPath: path,
            newPath: input
          });
        }

      }
    },
    watch: {
      files: function (value) {
        console.log(value)
      }
    },
    mounted() {

    }
  }
</script>


<style>
  #files div {
    font-size: 14px;
    padding: 8px 24px;
    background-color: transparent;
    color: black;
    cursor: pointer;
  }

  .active {
    background-color: black !important;
    color: white !important;
  }

  .input {
    position: absolute;
    bottom: 0;
    width: inherit;
  }
</style>
