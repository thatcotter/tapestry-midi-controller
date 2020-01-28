<template>
    <div id="wrapper">

        <h1>Midi Controller</h1>
        <h2>Last Note Sent: {{lastNote}}</h2>

        <table>
            <tr v-for="i in 8">
                <th v-for="j in 8">
                    <button 
                        v-on:click="sendMidiNote(getNoteIndex(i,j))">
                        {{getButtonID(getNoteIndex(i,j))}}
                    </button>
                </th>
            </tr>
        </table>

    </div>
</template>


<script lang="ts">
    import Vue from 'vue'
    import * as easymidi from 'easymidi';
    const noteIDmapping = require('../assets/button-mapping.json')
    
    const outputs = easymidi.getOutputs();
    const output = new easymidi.Output('virtualOut', true)

    export default{
        name: 'midi-buttons',
        data: function() {
            return {
                lastNote: 0,
            }
        },
        methods: {
            sendMidiNote(note) {
                this.lastNote = note
                console.log(this.lastNote)
                output.send('noteon', {
                    note: note,
                    velocity: 127,
                    channel: 1
                })
            },
            getNoteIndex(i, j) {
                return (j-1) + (i-1) * 8
            },
            getButtonID(note) {
                return noteIDmapping[`${note}`]
            }
        }
    }
</script>

<style lang="scss">
    @import url('https://fonts.googleapis.com/css?family=Source+Sans+Pro');

    * {
        box-sizing: border-box;
        margin: 0;
        padding: 0;
    }

    body { 
        font-family: 'Source Sans Pro', sans-serif;
    }

    #wrapper {
        height: 100vh;
        padding: 20px 50px;
        width: 100vw;
    }

    $button-colors: 
        #f44336, #FF5722, #4CAF50, #03A9F4,
        #0097A7, #673AB7, #E91E63, #90A4AE;

    $row-color: black;

    button {
        width: 10vw;
        height: 9.75vh;
        color: white;
        border: 0;
        border-radius: 20%;
        font-size: 24px;
    }

    @for $i from 1 through 8 {
        table{
            padding-top: 20px;
            tr {
                &:nth-child(#{$i}){
                    $row-color: nth($button-colors, $i);
                    button {
                        background-color: $row-color;
                    }
                }
            }
        }
    }
</style>