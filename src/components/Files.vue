<template>
  <div>
    <div style="width: 20%; float: left">
      <ul>
        <template v-for="(file, index, key) in files">
          <li @click.prevent="current = index">{{ index }}</li>
        </template>
      </ul>

    </div>
    <div style="width: 60%; float: left; padding-left: 20px;">
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
  import Edito from "./Edito";

  export default {
    name: 'files',
    components: {Edito},
    data: () => ({
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
        'Main.js': `Ana Main.js file with some code`,
        'Example.js': `Ana fichier akhor dyal code`
      },
    }),
    methods: {
      codeChanged: function (code) {
        this.files = {
          ...this.files,
          [this.current]: code
        }
      },
    },
    watch: {
      current: function (value) {
        this.files = {
          ...this.files,
          // [this.current]: 'Code that needs to be coming from the child component, Editor'
        }
      },
      files: function (value) {
        // console.log(this.files[this.current])
      }
    },
    mounted() {

    }
  }
</script>


<style>
  ul li {
    padding: 5px 0;
    border-bottom: 1px solid black;
  }
</style>
