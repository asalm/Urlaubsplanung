<template>
  <div class="formular">
    <h1 class="title">Urlaub beantragen</h1>

    <div class="field">
      <label class="label">Vorname</label>
      <div class="control">
        <input v-model="vorname" v-bind:class="{warning : vornameError}" class="input" id="vorname" type="text" placeholder="Maxi">
      </div>
      <p v-if="vornameError">Vor- und Nachname müssen ausgefüllt sein.</p>
    </div>

    <div class="field">
      <label class="label">Nachname</label>
      <div class="control">
        <input v-model="nachname" v-bind:class="{warning : nachnameError}" class="input" id="nachname" type="text" placeholder="Mustermensch">
      </div>
      <p v-if="nachnameError">Vor- und Nachname müssen ausgefüllt sein.</p>
    </div>

    <div class="field">
      <label class="label">Abteilung</label>
      <div class="control">
        <div class="select">
          <select v-model="abteilung" v-bind:class="{warning : abteilungError}" id="abteilung">
            <option>Digital</option>
            <option>Marketing</option>
            <option>Vertrieb</option>
          </select>
        </div>
      </div>
      <p v-if="abteilungError">Sie müssen eine Abteilung festlegen.</p>
    </div>

    <div class="field">
      <label class="label">Urlaubsart</label>
      <div class="control">
        <div class="select">
          <select v-model="urlaub" v-bind:class="{warning : urlaubError}" id="urlaub">
            <option>Erholungsurlaub</option>
            <option>Resturlaub</option>
            <option>Ausgleich</option>
          </select>
        </div>
      </div>
      <p v-if="urlaubError">Sie müssen eine Urlaubsart angeben.</p>
    </div>

    <div class="field">
      <label class="label-custom">Urlaub ab:</label><br>
      <div class="control">
        <input v-model="von" v-bind:class="{warning : vonError}" class="date" type="date" ref="inputVon">
      </div>
      <p v-if="vonError">Startzeitpunkt darf nicht vor dem aktuellen Datum liegen.</p>
    </div>
    
    <div class="field">
      <label class="label-custom">Urlaub bis:</label><br>
      <div class="control">
        <input v-model="bis" v-bind:class="{warning : bisError}" class="date" type="date" ref="inputBis">
      </div>
      <p v-if="bisError">Endzeitpunkt muss nach Startzeitpunkt sein.</p>
    </div>

    <div class="field is-grouped">
      <div class="control">
        <button class="button is-link" v-on:click="formValidierung">Einreichen</button>
      </div>
      <div class="control">
        <button class="button is-link is-light" v-on:click="reset">Abbrechen</button>
      </div>
    </div>
    <p class="text is-black">{{fullname}}.{{abteilung}}.{{urlaub}}.{{urlaubstage}}</p>
  </div>
    
  
</template>

<script>
export default {
  name: 'Formular',

  data: function() {
    return {
      vorname: '',
      vornameError: false,
      nachname: '',
      nachnameError: false,
      abteilung: '',
      abteilungError: false,
      urlaub: '',
      urlaubError: false,
      von: '',
      vonError: false,
      bis: '',
      bisError: false
    }
  },
  computed: {
    fullname: function(){
      return this.vorname + " " + this.nachname;
    },
    urlaubstage: function(){
      return this.tageBerechnen();
    }
  },
  methods: {
    tageBerechnen:function(){
      let tage;
      let start = this.von !== '' ? new Date(this.von):undefined;
      let ende = this.bis !== '' ? new Date(this.bis):undefined;
      if(start && ende){
        tage = (ende.getTime() - start.getTime()) / (1000 * 3600 * 24);
        return tage;
      }else{
        return "0";
      }  
    },
    datumStartValidierung:function(start){
      start = new Date(start);
      let now = Date.now();
      if(start < now){
        this.vonError = true;
        //this.$refs.inputVon.classList.add("warning");
      } else {
        this.vonError = false;
        //this.$refs.inputVon.classList.remove("warning");
      }
    },
    datumEndeValidierung:function(start, ende){
      if(ende < start && start !== ""){
        this.bisError = true;
        //this.$refs.inputBis.classList.add("warning");
      }else{
        this.bisError = false;
        //this.$refs.inputBis.classList.remove("warning");
      }
    },
    formValidierung:function(){
      if(this.vorname === ""){
        this.vornameError = true;
        return;
      }
      if(this.nachname === ""){
        this.nachnameError = true;
        return;
      }
      if(this.abteilung === ""){
        this.abteilungError = true;
        return;
      }
      if(this.urlaub === ""){
        this.urlaubError = true;
        return;
      }
      
      if(this.urlaubstage <= 0){
        this.vonError = true;
        this.bisError = true;
        return;
      }
      
      //if all tests pass ->
      let json = {
        name: this.fullname,
        dept: this.abteilung,
        from: this.von,
        till: this.bis,
        days: this.urlaubstage,
        type: this.urlaub,
        date: Date.now()
      }
      alert(JSON.stringify(json));
    },
    reset:function(){
      this.vorname = '';
      this.nachname ='';
      this.abteilung = '';
      this.urlaub ='';
      this.von = '';
      this.bis = '';
    }
  },
  watch: {
    von(start){
      this.datumStartValidierung(start);    
    },
    bis(ende){
      this.datumEndeValidierung(this.von, ende);
    },
    vorname(vor){
      if(vor.length > 1){
        this.vornameError = false;
      }
    },
    nachname(nach){
      if(nach.length > 1){
        this.nachnameError = false;
      }
    },
    abteilung(abt){
      if(abt.length > 1){
        this.abteilungError = false;
      }
    },
    urlaub(url){
      if(url.length > 1){
        this.urlaubError = false;
      }
    }
  }
}
</script>

<style scoped>
.label-custom{
  color: #363636;
  font-size: 1rem;
  font-weight: 700;
}
.date {
  margin-top:0.5em;
  margin-bottom:0.5em;
  width:100%;
  height:2.5em;
  color:#363636;
  border: 1px solid  hsl(0, 0%, 86%);
  border-radius: 5px;
  font-size:1rem;
  padding-left:1em;
  font-family: Avenir, Helvetica, Arial, sans-serif;
}
.warning{
  border-color: hsl(348, 100%, 61%);
}
.date:hover {
  border-color: hsl(0, 0%, 71%);
}
.date:focus{
  border-color: hsl(204, 86%, 53%);
  border-radius:5px;
}
.formular {
  max-width:600px;
  margin-left:1em;
}
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
