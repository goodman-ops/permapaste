<style scoped>
/* Move to global ? */
.content-padding {
  padding: 0px 5px;
}

.content-layout {
  display: block;
}
.content-font {
  font-size: 0.75rem;
}

.select-section {
  display: flex;
  flex-direction: column;
  align-items: center;
  align-content: flex-start;
  justify-content: start;
  padding: 1rem 0 0 0;
  min-height: 23rem;
  overflow: auto;
}

.select-section > * {
  flex-grow: 0;
  flex-shrink: 0;
}

.card {
  display: block;
  width: 90%;
  max-width: 600px;
  margin: 1rem;
  border-radius: 5px;
  box-shadow: 0 2px 5px 0 rgba(0,0,0,0.16),0 2px 10px 0 rgba(0,0,0,0.12);
  overflow: hidden;
  transition: height 0.56s;
  will-change: height;
}

/* Works but for consistency with other buttons disabled */
/*.card button:active {
  outline: none;
}*/

.card p {
  font-size: 0.85rem;
  text-align: center;
  height: 0px;
  margin: 0px;
  padding: 0px;
  /*overflow: hidden;*/
  /*transition: height 0.06s;
  will-change: height;*/

  overflow: hidden;
  transition: height 0.56s;
  will-change: height;
}

.card button .tick {
  float: right;
  color: rgba(255,255,255,0); 
  line-height: 100%;
  transition: color 0.45s;
}
.card button .tick::after {
  content: "\2713"; /* Tick symbol */
}

.selected-card {
  border: 2px solid rgba(142, 141, 238, 0.885);
  /*box-shadow: 0 0 1.6em 1px rgba(47, 27, 219, 0.363); */
}

/** Card buttons */
.card button {
  font-size: 0.85em;
  display: block;
  width: 100%;
  background: rgb(173, 182, 174);
}
.card button:active {
  transform: unset;
}
.selected-card button {
  background: rgb(80, 110, 140, 1);
 }

.selected-card p {
  background: white;
  color: #222;
  height: initial;
  padding: 16px 10px;
}

.selected-card button .tick {
  color: rgba(255,255,255,1)
}

.step-footer {
  display: flex;
  justify-content: space-between;
  padding-top: 0.5em;
  margin: 3em auto 0 auto;
  width: 90%;
  max-width: 600px;
}

.step-footer button {
  flex-basis: 50%;
}

.password-input {
  display: flex;
  align-items: center;
  justify-content: center;
}

.password-input button {
  display: unset;
  width: unset; 
  background: rgba(0,0,0,0); 
  border: 1px solid rgba(138,138,138, 0.25);
  border-radius: 1em;
  padding: 0.7em;
  margin: 0.5em;
}
.password-input button .eyeball::after {
  content: "\01F441";
}


.wallet-load {
  width: 90%;
  max-width: 600px;
  border: 1px dashed #ccc;
}

</style>

<template>
  <div class="content-padding content-layout">
    
    <div class="select-section">
      
      <p> Select Privacy Setting </p>
      
      <div class="card" v-bind:class="{ 'selected-card': selected === 'public'}">
        <button @click="select($event, 'public')"> 
          Public 
          <span class="tick"></span>
        </button>
        <p> Paste will be readable by anyone </p>
      </div>

      <div class="card" v-bind:class="{ 'selected-card': selected === 'secretpass'}">
        <button @click="select($event, 'secretpass')"> 
          Encrypt with password 
          <span class="tick"></span>
        </button>
        <p> 
          Paste will need to be unlocked with a password. This is 
          good for keeping documents private to yourself.
          You can search for them later by wallet address or block number to edit and save a new version. 
          <br/>
          <br/>
          <b>Make sure to use a strong passphrase!</b>
        </p>
        <p class="password-input">
          <input autocomplete="new-password" placeholder="Enter a strong passphrase" v-model="editing.pasteOptions.password" v-bind:type="pwVisible ? 'text': 'password'"/>
          <button class="secondary-btn" @click="pwVisible = !pwVisible"><span class="eyeball"></span></button>
        </p>
      </div>

      <div class="card" v-bind:class="{ 'selected-card': selected === 'secretlink'}">
        <button @click="select($event, 'secretlink')"> 
          Encrypt with secret link
          <span class="tick"></span>
        </button>
        <p> 
          Paste can be read by anyone who has the link. 
          <br/>
          When the link is lost, access will be lost. 
          <br/>
          This is good for sharing documents with one or more people privately. 
          <br/>
          <br/>
          <span style="font-style: italic">If this link is leaked, anyone can get access to the document. </span>
        </p>
      </div>
      
    </div>

    <div class="step-footer">
      <button @click="$router.replace('/paste/edit/')" class="secondary-btn">Back to Editing</button>
      <button @click="$router.replace('/paste/finalize/')">Next</button>
    </div>

  </div>  
</template>

<script lang="ts">
import Vue from 'vue'
import Component from 'vue-class-component';
import { globalStore } from '../app-state';

@Component
export default class extends Vue {

  pwVisible = false; 
  editing = globalStore.PasteEditing;

  select(ev: any, which: 'public' | 'secretpass' | 'public') {
    this.editing.paste.pastePrivacy = which
    ev.target.blur()
  }

  get selected(): string { 
    return this.editing.paste.pastePrivacy
  }
}
</script>