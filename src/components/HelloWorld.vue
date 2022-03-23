<template>
    <div class="hello">
        <b-container>
            <b-row>
                <b-col>
                    <h1>{{ msg }}</h1>
                </b-col>
            </b-row>
            <b-row>
                <b-col md="4">
                    <b-button block class="mb-2" @click="lmsInitialize()">Initialize</b-button>
                    <b-button block class="mb-2" @click="readSuspendData()">Read SuspendData</b-button>
                    <b-button block class="mb-2" @click="updateSuspendData()">Write SuspendData</b-button>
                    <b-button block class="mb-2" @click="setCompletion()">setCompletion</b-button>
                    <b-button block class="mb-2" @click="setSuccessStatus()">setSuccessStatus</b-button>
                    <b-button block class="mb-2" @click="lmsSave()">Save</b-button>
                    <b-button block class="mb-2" @click="lmsQuit()">Quit</b-button>
                    <b-button block class="mb-2" @click="mergeArrays()">Merge</b-button>
                </b-col>
                <b-col md="8">
                    <b-form-textarea id="textarea" v-model="text" rows="10"></b-form-textarea>
                </b-col>
            </b-row>
        </b-container>
    </div>
</template>

<script>
import { SCORM } from "pipwerks-scorm-api-wrapper";

export default {
    name: "HelloWorld",
    props: {
        msg: String,
    },
    data() {
        return {
            text: "",
        };
    },
    methods: {
        lmsInitialize() {
            var status = SCORM.init();
            if (status) {
                // otherwise it's gonna set completion=completed and Success=passed if you just open and close the course
                this.lmsSet({
                    key: "cmi.exit",
                    value: "suspend",
                });
                this.lmsSave();
            }
            console.log("### lmsInitialize: ", status);
        },

        lmsSet(payload) {
            var key = payload.key;
            var value = payload.value;

            var status = SCORM.set(key, value);
            console.log("### lmsSet: ", payload, status);
        },

        lmsGet (payload) {
            var returnValue = SCORM.get(payload);

            console.log("### lmsGet: ", payload, returnValue);
            return returnValue;
        },

        lmsSave () {
            var returnValue = SCORM.save();

            console.log("### lmsSave: ", returnValue);
        },

        lmsQuit () {
            var status = SCORM.quit();
            console.log("### lmsQuit: ", status);
        },

        updateSuspendData() {
            this.lmsSet({
                key: "cmi.suspend_data",
                value: this.text,
            });
        },

        readSuspendData() {
            this.text = this.lmsGet("cmi.suspend_data");
        },

        setCompletion() {
             this.lmsSet({
                key: "cmi.completion_status",
                value: "completed",
            });
        },

        setSuccessStatus() {
             this.lmsSet({
                key: "cmi.success_status",
                value: "passed",
            });
        },

        mergeArrays() {

            //  array stored in the page - full dataset
            var pageArray = [
                {
                    id: "1",
                    title: "Title 1",
                    subtitle: "Subtitle 1"
                },
                {
                    id: "2",
                    title: "Title 2",
                    subtitle: "Subtitle 2"
                },
                {
                    id: "3",
                    title: "Title 3",
                    subtitle: "Subtitle 3"
                },
            ]

            //  array with just the ids comming from the suspendData
            var suspendDataArray = [
                {
                    id: "1"
                },
                {
                    id: "2"
                }
            ]

            //  final merged array
            var mergedArray = [];

            suspendDataArray.forEach(elementA => {
                pageArray.forEach(elementB => {
                    if (elementA.id == elementB.id) {
                        mergedArray.push(elementB);
                    }
                });
            });
                
            console.log ("mergedArray: ", mergedArray);

        }
    },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
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
