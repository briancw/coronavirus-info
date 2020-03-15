<template>
    <div id="home">
        <h1>Resources</h1>

        <h3>Data</h3>
        <a href="https://www.worldometers.info/coronavirus/">WorldMeters Information</a>
        <br>

        <h3>Maps</h3>
        <a href="https://www.nytimes.com/interactive/2020/world/coronavirus-maps.html">New York Times Map</a>
        <br>
        <a href="https://covid19info.live/">Covid19Info.live</a>
        <br>
        <a href="https://www.arcgis.com/apps/opsdashboard/index.html#/bda7594740fd40299423467b48e9ecf6">John Hopkins Univeristy Map</a>
        <br>

        <h3>News</h3>
        <a href="https://www.reddit.com/r/Coronavirus/">Coronavirus Subreddit</a>
        <br>

        <h3>Prediction Tools</h3>
        <div class="prediction1">
            According to Michael Osterholm, the cases double roughly every 4 days.<br>
            5% of cases require a ventilator<br>
            The US currently has 160,000 ventilators<br>
            <br>

            Start Cases: <input type="text" v-model="startCases" />
            Days to Double: <input type="text" v-model="doubleTime" />
            Ventilators on hand: <input type="text" v-model="ventilatorCount" />
            Percent requiring a ventilator: <input type="text" v-model="ventilatorPercent" />
            Death Rate <input type="text" v-model="deathRate" />
            <br>
            <br>

            <table>
                <thead>
                    <td>Date</td>
                    <td>Days Out</td>
                    <td>Cases</td>
                    <td>% of population</td>
                    <td>Ventilators Needed</td>
                    <td>Ventilator Shortage</td>
                    <td>Deaths</td>
                </thead>
                <tbody>
                    <tr v-for="cases in casesTable">
                        <td>{{cases.dateString}}</td>
                        <td>{{cases.dayOut}}</td>
                        <td>{{cases.number}}</td>
                        <td>{{cases.popPercent}}</td>
                        <td>{{cases.ventilatorsNeeded}}</td>
                        <td>{{cases.ventilatorShortage}}</td>
                        <td>{{cases.deaths}}</td>
                    </tr>
                </tbody>
            </table>
            <!-- <div class="casesPrediction" v-html="casesString"></div> -->
        </div>
    </div>
</template>

<script>

export default {
    name: 'home',
    data() {
        return {
            startCases: 2600,
            ventilatorPercent: 5,
            ventilatorCount: 160000,
            doubleTime: 4,
            deathRate: 2.3,
            population: 327000000,
            daysToCalculate: 64,
        }
    },
    computed: {
        casesTable() {
            let casesData = []
            let currentCases = this.startCases
            const today = new Date()
            
            for (let i = 0; i <= this.daysToCalculate; i += 1) {
                if (i % this.doubleTime === 0) {
                    let currentDate = new Date(today.getTime() + (1000 * 60 * 60 * 24 * i))
                    let dateString = `${String(currentDate.getMonth() + 1)}-${String(currentDate.getDate()).padStart(2, '0')}`
                    let caseNumbePadded = Math.round(currentCases).toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",")
                    let ventilatorsNeeded = currentCases * (this.ventilatorPercent / 100)
                    let ventilatorShortage = (ventilatorsNeeded - this.ventilatorCount) < 0 ? 0 : (ventilatorsNeeded - this.ventilatorCount)
                    let deaths = currentCases * (this.deathRate / 100)
                    let popPercent = Math.round(currentCases / this.population * 100)
                    
                    casesData.push({
                        number: caseNumbePadded,
                        dayOut: i,
                        dateString,
                        popPercent,
                        ventilatorsNeeded: ventilatorsNeeded.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ","),
                        ventilatorShortage: ventilatorShortage.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ","),
                        deaths: deaths.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ","),
                    })
                    currentCases *= 2
                }
            }

            return casesData
        }
    }
}
</script>

<style lang="less">
@import '../styles/mixins.less';
#home {
    text-align: center;
}
h3 {
    margin-top: 20px;
}
.prediction1 {
    display: inline-block;

    input {
        width: 50px;
    }
    table {
        display: inline;
    }
    tr {
        border: 1px solid #000;
    }
    td {
        padding: 0px 10px;
    }
}
</style>