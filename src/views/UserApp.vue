<script>
import axios from 'axios';

export default {
    data() {
        return {
            date: new Date(),
            dateLesson: new Date(),
            trueNnamePleers: '',

            timeLesson: [
                {
                    "ID": "1",
                    "timeBeginning": "09:00:00",
                    "timeEnd": "09:45:00",
                    "сheckСall": "1",
                    "melody": "https://alexbruni.ru/afx/sound_file/zvon-shkolnogo-kolokolchika-posledniy-zvonok-66.mp3",
                    "college_id": "1"
                },
                {
                    "ID": "2",
                    "timeBeginning": "09:55:00",
                    "timeEnd": "10:40:00",
                    "сheckСall": "1",
                    "melody": "https://alexbruni.ru/afx/sound_file/zvon-shkolnogo-kolokolchika-posledniy-zvonok-66.mp3",
                    "college_id": "1"
                },
                {
                    "ID": "3",
                    "timeBeginning": "11:00:00",
                    "timeEnd": "11:45:00",
                    "сheckСall": "1",
                    "melody": "https://alexbruni.ru/afx/sound_file/zvon-shkolnogo-kolokolchika-posledniy-zvonok-66.mp3",
                    "college_id": "1"
                },
                {
                    "ID": "4",
                    "timeBeginning": "11:55:00",
                    "timeEnd": "12:40:00",
                    "сheckСall": "1",
                    "melody": "https://alexbruni.ru/afx/sound_file/zvon-shkolnogo-kolokolchika-posledniy-zvonok-66.mp3",
                    "college_id": "1"
                },
                {
                    "ID": "5",
                    "timeBeginning": "13:00:00",
                    "timeEnd": "13:45:00",
                    "сheckСall": "1",
                    "melody": "https://alexbruni.ru/afx/sound_file/zvon-shkolnogo-kolokolchika-posledniy-zvonok-66.mp3",
                    "college_id": "1"
                },
                {
                    "ID": "6",
                    "timeBeginning": "14:05:00",
                    "timeEnd": "14:50:00",
                    "сheckСall": "1",
                    "melody": "https://alexbruni.ru/afx/sound_file/zvon-shkolnogo-kolokolchika-posledniy-zvonok-66.mp3",
                    "college_id": "1"
                },
                {
                    "ID": "7",
                    "timeBeginning": "15:10:00",
                    "timeEnd": "15:55:00",
                    "сheckСall": "1",
                    "melody": "https://alexbruni.ru/afx/sound_file/zvon-shkolnogo-kolokolchika-posledniy-zvonok-66.mp3",
                    "college_id": "1"
                }
            ],
        }
    },

    mounted() {
        this.startTimer();
        this.received_formattingData();
        // this.add_mySQL();

        document.addEventListener('keydown', function (event) {
            if (event.code == 'Space') {
                let audio = document.querySelector("audio");
                audio.volume = 0.2;
                audio.play();
            };
        });
    },
    methods: {
        playAudio() {
            let audio = document.querySelector("audio");
            audio.volume = 0.2;
            audio.play();
        },

        // Работа с серваком 
        async add_mySQL() {
            let response = await axios.get('/');
            console.log(response);
            this.timeLesson = response.data;

            this.received_formattingData();
        },

        received_formattingData() {
            for (let i = 0; i < this.timeLesson.length; i++) {
                this.timeLesson[i].timeBeginning = this.timeLesson[i].timeBeginning.slice(0, 5);
                this.timeLesson[i].timeEnd = this.timeLesson[i].timeEnd.slice(0, 5);
            };
            this.trueNnamePleers = this.timeLesson[0].melody
            console.log(this.trueNnamePleers);
        },

        changeTime_counter(startTime, endTime) {
            // Разбиваем время на часы и минуты
            const startParts = startTime.split(':');
            const endParts = endTime.split(':');

            // Преобразуем в минуты
            const startMinutes = startParts[0] * 60 + parseInt(startParts[1]);
            const endMinutes = endParts[0] * 60 + parseInt(endParts[1]);

            // Вычисляем разницу
            let difference = endMinutes - startMinutes;

            // Обрабатываем отрицательную разницу
            if (difference < 0) {
                difference += 24 * 60;
            };

            return difference;
        },

        startTimer() {
            window.setInterval(() => {
                this.date = new Date()
            }, 1000);

            window.setInterval(() => {
                this.dateLesson = new Date()
            }, 60000);
        },

        CheckingZeroAdditionTime(getTupeDate) {
            if (getTupeDate < 10) {
                return "0" + getTupeDate;
            } else {
                return getTupeDate;
            };
        },


        timeEventListener_calls(index) {
            let timeBeginning = this.timeLesson[index].timeBeginning;
            let startTime;
            index - 1 === -1 ?
                startTime = this.timeLesson[index].timeBeginning :
                startTime = this.timeLesson[index - 1].timeEnd;
            let endTime = this.timeLesson[index].timeEnd;

            // Настоящее время
            let time = `${this.CheckingZeroAdditionTime(this.dateLesson.getHours())}:${this.CheckingZeroAdditionTime(this.dateLesson.getMinutes())}`;
            // let time = '13:00';


            // Разбиваем время на часы и минуты
            const startParts = startTime.split(':');
            const endParts = endTime.split(':');
            const timeParts = time.split(':');
            const timeBeginningParts = timeBeginning.split(':');

            // Преобразуем в минуты
            const startMinutes = startParts[0] * 60 + parseInt(startParts[1]);
            const endMinutes = endParts[0] * 60 + parseInt(endParts[1]);
            const timeMinutes = timeParts[0] * 60 + parseInt(timeParts[1]);
            const timeBeginningMinutes = timeBeginningParts[0] * 60 + parseInt(timeBeginningParts[1]);

            // Проверка на проигрования звонка
            if ((timeMinutes == endMinutes || timeMinutes == timeBeginningMinutes) && this.timeLesson[index].сheckСall == "1") {
                this.playAudio();
            };

            // Проверка на подстановку стилей
            switch (true) {
                case endMinutes <= timeMinutes:
                    return 'past';
                case startMinutes <= timeMinutes && timeMinutes < endMinutes:
                    return 'active';
                case startMinutes > timeMinutes:
                    return "planet";
            };
        }
    }
};
</script>




<template>
    <header class="row">
        <!-- Название организации -->
        <div class="name_college col">Басовская</div>

        <!-- Часы -->
        <div class="time col">
            {{ CheckingZeroAdditionTime(this.date.getHours()) }}:
            {{ CheckingZeroAdditionTime(this.date.getMinutes()) }}:
            {{ CheckingZeroAdditionTime(this.date.getSeconds()) }}
        </div>

        <!-- Дата -->
        <div class="date col">
            {{ CheckingZeroAdditionTime(this.date.getDate()) }}.
            {{ CheckingZeroAdditionTime(this.date.getMonth() + 1) }}.
            {{ this.date.getFullYear() }}
        </div>
    </header>

    <h2>Расписание <br> Звонков</h2>


    <el-container class="schedule">

        <div v-for=" (i, index) in timeLesson" class="container">

            <el-row class="lesson container align-items-center" :class="timeEventListener_calls(index)">
                <div class="num_lesson col-auto">
                    {{ i.ID }} урок
                </div>
                <div class="col">
                    <hr>
                </div>
                <div class="col-auto">
                    {{ i.timeBeginning }}-{{ i.timeEnd }}
                </div>
            </el-row>

            <el-row v-if="i.ID != this.timeLesson.length" class="change container align-items-center ">
                <div class="col">
                    <hr>
                </div>
                <div class="col-auto">
                    {{ changeTime_counter(this.timeLesson[index].timeEnd, this.timeLesson[index + 1].timeBeginning) }}
                    мин
                </div>
                <div class="col">
                    <hr>
                </div>
            </el-row>
        </div>

        <button type="button" class="button_call btn btn-secondary" @click="playAudio()">
            Звонить
        </button>
    </el-container>

    <!-- Проигрователь музыки -->
    <!-- https://alexbruni.ru/afx/sound_file/zvon-shkolnogo-kolokolchika-posledniy-zvonok-66.mp3  - колокольчик -->
    <!-- https://alexbruni.ru/afx/sound_file/zvuk-yaponskogo-shkolnogo-zvonka-elektronnyy-77.mp3  - спокойный -->
    <!-- https://alexbruni.ru/afx/sound_file/korotkiy-zvonok-shkolnogo-zvonka-35.mp3              - класичиский -->
    <audio :src="this.trueNnamePleers"></audio>
</template>






<style scoped>
* {
    margin: 0;
    padding: 0;
}

body {
    width: 1024px;
    height: 768px;
    max-width: 750px;
    margin: auto;
}

h2 {
    font-size: 4vh;
    margin-top: 35px;
    margin-bottom: 20px;
    text-align: center;
}

header {
    margin: 10px;
    margin-top: 20px;
}

header div {
    font-size: 2.7vh;
}

header .time {
    text-align: center;
    font-size: 3.5vh;
}

header .date {
    text-align: end;
}

.schedule {
    text-align: center;
    margin-top: 15px;
}

.lesson,
.change {
    min-width: 300px;
    width: 100%;
    padding: 0 10px 0 10px;
    margin-bottom: 1px;
    font-size: 2vh;
}

.lesson .col,
.change .col {
    padding: 10px;
}

.lesson {
    color: #ffffff;
    font-weight: 600;
    height: 6vh;
    border-radius: 16px;
}

.change {
    height: 3vh;
}

.schedule {
    display: flex;
    margin: auto;
    flex-direction: column;
    align-content: space-around;
    flex-wrap: wrap;
    justify-content: center;
    align-items: stretch;
}

.button_call {
    margin: 20px;
    height: 6vh;
    font-size: 2vw;
}

/* подставные класы */
.past {
    background-color: rgb(87, 133, 208);
}

.active {
    background-color: rgb(0, 0, 255);
}

.planet {
    background-color: rgb(255, 145, 0);
}
</style>
