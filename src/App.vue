<template>
    <ThemeSwitcher />
    <div class="card">
        <h4 class="card-title">Islomiy namoz vaqtlari</h4>
        <p class="card-body">Samarqand · {{  today }} </p>
        <Tabs value="0">
            <TabList>
                <Tab value="0">Bugun</Tab>
                <Tab value="1">Haftalik</Tab>
                <Tab value="2">30 kunlik</Tab>
            </TabList>
            <TabPanels>
                <TabPanel value="0">
                    <DataTable :value="prayerArray" tableStyle="min-width: 50rem">
                        <Column field="tong_saharlik" header="Tong"></Column>
                        <Column field="quyosh" header="Quyosh"></Column>
                        <Column field="peshin" header="Peshin"></Column>
                        <Column field="asr" header="Asr"></Column>
                        <Column field="shom_iftor" header="Shom"></Column>
                        <Column field="hufton" header="Xufton"></Column>
                    </DataTable>
                </TabPanel>
                <TabPanel value="1">
                    <DataTable :value="prayerWeekArray" tableStyle="min-width: 50rem">
                        <Column field="date" header=""></Column>
                        <Column field="times.tong_saharlik" header="Tong"></Column>
                        <Column field="times.quyosh" header="Quyosh"></Column>
                        <Column field="times.peshin" header="Peshin"></Column>
                        <Column field="times.asr" header="Asr"></Column>
                        <Column field="times.shom_iftor" header="Shom"></Column>
                        <Column field="times.hufton" header="Xufton"></Column>
                    </DataTable>
                </TabPanel>
                <TabPanel value="2">
                    <DataTable :value="prayerMonthArray" paginator :rows="5" :rowsPerPageOptions="[5, 10]"
                        tableStyle="min-width: 50rem">
                        <Column field="day" header=""></Column>
                        <Column field="times.tong_saharlik" header="Tong"></Column>
                        <Column field="times.quyosh" header="Quyosh"></Column>
                        <Column field="times.peshin" header="Peshin"></Column>
                        <Column field="times.asr" header="Asr"></Column>
                        <Column field="times.shom_iftor" header="Shom"></Column>
                        <Column field="times.hufton" header="Xufton"></Column>
                    </DataTable>
                </TabPanel>
            </TabPanels>
        </Tabs>
        
    </div>
</template>
<script>
    import {
        data
    }

    from "autoprefixer";
    import axios from "axios";
    import {
        useGeolocation
    } from '@vueuse/core'

    export default {
        data() {
            return {
                today: '',
                prayerTimes: null,
                prayerArray: null,
                prayerMonthArray: null,
                prayerWeekArray: null,
                city: 'Samarqand',
                month: null,
                location: null,
                gettingLocation: false,
                errorStr: null,
                position: null,
                latitude: null,
                longitude: null,
                locationError: false,
                errorMessage: ''
            }
        },
        methods: {
            TimeDate() {
                const monthNames = ["Yanvar",
                    "Fevral",
                    "Mart",
                    "Aprel",
                    "May",
                    "Iyun",
                    "Iyul",
                    "August",
                    "Sentabr",
                    "Oktyabr",
                    "Noyabr",
                    "Dekabir"
                ];

                const current = new Date();

                this.today = `${current.getDate()}-${monthNames[current.getMonth()]},${current.getFullYear()}`;
                this.month = current.getMonth()
            },
            locationCity() {
                navigator.geolocation.getCurrentPosition(
                    position => {
                        axios.get("https://api.bigdatacloud.net/data/reverse-geocode-client?latitude="+this.latitude+"&longitude="+this.longitude+"&localityLanguage=uz").then((response) => {
                            this.city = response.data.city
                        });
                    },
                    error => {
                        console.log(error.message);
                    },
                )
            }
        },
        mounted() {
            this.TimeDate()
            this.locationCity()

            axios.get('https://islomapi.uz/api/present/day?region=' + this.city).then((response) => {
                    this.prayerArray = [response.data.times];
                }

            ).catch((error) => {
                    console.error('Error fetching prayer times:', error);
                }

            );

            axios.get('https://islomapi.uz/api/monthly?region=' + this.city + '&month=' + this.month).then((
                response) => {
                this.prayerMonthArray = response.data;
            }).catch((error) => {
                    console.error('Error fetching prayer times:', error);
                }

            );

            axios.get('https://islomapi.uz/api/present/week?region=' + this.city).then((response) => {
                this.prayerWeekArray = response.data;
            }).catch((error) => {
                console.error('Error fetching prayer times:', error);
            });
        }
    }
</script>
<style scoped>
    .card-title {
        margin-left: 16px;
        color: #202124;
        font-family: Google Sans, Arial, sans-serif;
        font-size: 22px;
        font-weight: 400;
        line-height: 28px;
    }

    .card-body {
        color: #70757a;
        font-family: Arial, sans-serif;
        font-size: 14px;
        margin-left: 16px;
        padding: 4px 0 9px;
    }

    .tab-time {
        font-size: 36px;
        line-height: 44px;
        color: #202124;
    }
</style>