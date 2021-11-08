<template>
  <div class="form" @click="closeUl">
    <form>
      <h2 class="error" v-if="russianSymbolsError">Поля: Фамилия, Имя, Отчество, Предыдущая Фамилия, Предыдущее Имя должны содержать только русские буквы!</h2>
      <h2 class="error" v-if="birthdayError">Введите валидную дату рождения!</h2>
      <h2 class="error" v-if="emailError">Введите валидный email!</h2>
      <h2 class="error" v-if="seriesPassError && formData.cShip === 'Russia'">Серия паспорта состоит из 4 цифр!</h2>
      <h2 class="error" v-if="numberPassError && formData.cShip === 'Russia'">Номер паспорта состоит из 6 цифр!</h2>
      <h2 class="error" v-if="latinSymbolsError && formData.cShip !== 'Russia'">Поля: Фамилия на латинице и Имя на латинице должны содержать только латинские буквы! </h2>
      <h2 class="error" v-if="needToFill">Заполните поля!</h2>
      <h1>Личные данные</h1>
      <div class="row">
        <div class="column">
          <label for="surname">Фамилия</label>
          <input class="text" type="text" id="surname" v-model="formData.surname" autocomplete="off" @blur="validateRussianSymbols">
        </div>
        <div class="column">
          <label for="name">Имя</label>
          <input class="text" type="text" id="name" v-model="formData.name" autocomplete="off" @blur="validateRussianSymbols">
        </div>
        <div class="column">
          <label for="patronymic">Отчество</label>
          <input class="text" type="text" id="patronymic" v-model="formData.patronymic" autocomplete="off" @blur="validateRussianSymbols">
        </div>
      </div>
      <div class="column">
        <label for="bDate">Дата рождения</label>
        <input class="text" type="date" id="bDate" placeholder="дд.мм.гггг" v-model="formData.bDate" @blur="validateBirthday">
      </div>
      <div class="column">
        <label for="email">E-mail</label>
        <input class="text" type="email" id="email" v-model="formData.email" autocomplete="off" @blur="validateEmail">
      </div>
      <h3>Пол</h3>
      <div class="row">
        <input class="radio" type="radio" id="male" name="sex" value="male" v-model="formData.sex">
        <label for="male">Мужской</label>

        <input class="radio" type="radio" id="female" name="sex" value="female" v-model="formData.sex">
        <label for="female">Женский</label>
      </div>
      <h3>Паспортные данные</h3>
      <div class="column">
        <label for="cShip">Гражданство</label>
        <input class="text" @click="toggleList" id="cShip" v-model="searchWord" autocomplete="off">
      </div>
      <ul v-if="toggle">
        <li v-for="(item, idx) in filteredcShips" :key="idx" @click="onClick(item)">{{item}}</li>
      </ul>
      <div class="row" v-if="formData.cShip === `Russia`">
        <div class="column">
          <label for="sPass">Серия паспорта</label>
          <input class="text" type="number" id="sPass" v-model="formData.sPass" @blur="validateSeriesPass">
        </div>
        <div class="column">
          <label for="nPass">Номер паспорта</label>
          <input class="text" type="number" id="nPass" v-model="formData.nPass" @blur="validateNumberPass">
        </div>
        <div class="column">
          <label for="dPass">Дата выдачи</label>
          <input class="text" type="date" id="dPass" v-model="formData.dPass">
        </div>
      </div>
      <div v-else-if="formData.cShip" class="column">
        <div class="row">
          <div class="column">
            <label for="latinSurname">Фамилия на латинице</label>
            <input class="text" type="text" id="latinSurname" v-model="formData.latinSurname" @blur="validateLatinSymbols">
          </div>
          <div class="column">
            <label for="latinName">Имя на латинице</label>
            <input class="text" type="text" id="latinName" v-model="formData.latinName" @blur="validateLatinSymbols">
          </div>
        </div>
        <h4 style="color: darkgrey">Иностранцы заполняют латинскими буквами. Например, Ivanov Ivan</h4>
        <div class="row">
          <div class="column">
            <label for="altPassNumber">Номер паспорта</label>
            <input class="text" type="number" id="altPassNumber" v-model="formData.altPassNumber">
          </div>
          <div class="column">
            <label for="country">Страна выдачи</label>
            <select id="country" v-model="formData.country">
              <option value="usa">США</option>
              <option value="france">Франция</option>
              <option value="italy">Италия</option>
              <option value="spain">Испания</option>
            </select>
          </div>
          <div class="column">
            <label for="passType">Тип паспорта</label>
            <select id="passType" v-model="formData.passType">
              <option v-for="item in passTypes" :key="item" :value="item">{{item}}</option>
            </select>
          </div>
        </div>
      </div>
      <h3>Меняли ли фамилию или имя?</h3>
      <div class="row">
        <input class="radio" type="radio" id="no" name="nameChange" value="no" v-model="formData.nameChange">
        <label for="no">Нет</label>

        <input class="radio" type="radio" id="yes" name="nameChange" value="yes" v-model="formData.nameChange">
        <label for="yes">Да</label>
      </div>
      <div class="row" v-if="formData.nameChange === 'yes'">
        <div class="column">
          <label for="lastSurname">Предыдущая фамилия</label>
          <input class="text" type="text" id="lastSurname" v-model="formData.lastSurname" @blur="validateRussianSymbols">
        </div>
        <div class="column">
          <label for="lastName">Предыдущее имя</label>
          <input class="text" type="text" id="lastName" v-model="formData.lastName" @blur="validateRussianSymbols">
        </div>
      </div>
      <button @click.prevent="sendData">Отправить</button>
    </form>
  </div>
</template>

<script>

import cShips from '../assets/data/citizenships.json'
import passTypes from '../assets/data/passport-types.json'
import {debounce} from "@/helpers/debounce";

export default {
  data() {
    return {
      cShips: [],
      filteredcShips:[],
      countries: [],
      passTypes: [],
      searchWord: null,
      debouncedCitizenship: null,
      toggle: false,
      russianSymbolsError: false,
      latinSymbolsError: false,
      birthdayError: false,
      emailError: false,
      seriesPassError: false,
      numberPassError: false,
      needToFill: false,
      formData: {
        surname: '',
        name: '',
        patronymic: '',
        bDate: '',
        email: '',
        sex: '',
        cShip: '',
        sPass: '',
        nPass: '',
        dPass: '',
        nameChange: '',
        lastSurname: '',
        lastName: '',
        latinName: '',
        latinSurname: '',
        altPassNumber: '',
        country: '',
        passType: '',
      }
    };
  },
  created() {
    cShips.forEach((item) => {
      this.cShips.push(item.nationality)
      this.filteredcShips.push(item.nationality)
    })
    passTypes.forEach((item) => {
      this.passTypes.push(item.type)
    })
    this.debouncedCitizenship = debounce(this.getCitizenship, 500)
  },
  methods: {
    toggleList() {
      this.toggle = true
    },
    onClick(item) {
      this.formData.cShip = item
      this.searchWord = item
      this.toggle = false
    },
    closeUl(event) {
      if(event.target.id !== "cShip") {
        this.toggle = false
      }
    },
    getCitizenship(searchWord) {
      this.filteredcShips = this.cShips.filter((item) => item.toLowerCase().includes(searchWord.toLowerCase()))
      this.formData.cShip = searchWord
      console.log("to server:", searchWord)
    },
    sendData() {
      this.needToFill = false
      const err = document.querySelector(".err")
      if (err) {
        console.log('Устраните ошибки при заполнении полей!')
      } else if (!this.unfilled) {
        console.log(JSON.stringify(this.formData))
      } else {
        this.needToFill = true
        console.log('Заполните поля!')
      }
    },
    validateRussianSymbols(event) {
      this.russianSymbolsError = false
      const REG_EXP = /[a-z A-Z0-9-().,@&#*!%:'"`~|/=+]+/
      const validation = event.target.value.match(REG_EXP)
      if(validation) {
        this.russianSymbolsError = true
        event.target.classList.add('err')
      } else {
        event.target.classList.remove('err')
      }
    },
    validateLatinSymbols(event) {
      this.latinSymbolsError = false
      const REG_EXP = /[а-я А-Я0-9-().,@&#*!%:'"`~|/=+]+/
      const validation = event.target.value.match(REG_EXP)
      if(validation) {
        this.latinSymbolsError = true
        event.target.classList.add('err')
      } else {
        event.target.classList.remove('err')
      }
    },
    validateBirthday(event) {
      this.birthdayError = false
      const today = Date.now()
      const stringToday = new Date(today)
      let day = stringToday.getDate()
      if (day.toString().length === 1) {
        day = Number(`0${day.toString()}`)
      }
      const month = stringToday.getMonth() + 1
      const year = stringToday.getFullYear()

      const myDay = Number(event.target.value.slice(-2))
      const myMonth = Number(event.target.value.slice(5, 7))
      const myYear = Number(event.target.value.slice(0, 4))
      if ((myYear > year) || ((myMonth > month) && (myYear === year)) || ((myDay > day) && (myMonth === month) && (myYear === year))) {
        this.birthdayError = true
        event.target.classList.add('err')
      } else {
        event.target.classList.remove('err')
      }
    },
    validateEmail(event) {
      this.emailError = false
      const REG_EXP = /^[A-Z0-9._%+-]+@[A-Z0-9-]+.+.[A-Z]{2,4}$/i
      const validation = event.target.value.match(REG_EXP)
      if (!validation) {
        this.emailError = true
        event.target.classList.add('err')
      } else {
        event.target.classList.remove('err')
      }
    },
    validateSeriesPass(event) {
      this.seriesPassError = false
      if (event.target.value.toString().length !== 4) {
        this.seriesPassError = true
        event.target.classList.add('err')
      } else {
        event.target.classList.remove('err')
    }
  },
    validateNumberPass(event) {
      this.numberPassError = false
      if (this.formData.cShip === "Russia") {
        if (event.target.value.toString().length !== 6) {
          this.numberPassError = true
          event.target.classList.add('err')
        } else {
          event.target.classList.remove('err')
        }
      }
    }
  },
  watch: {
    searchWord(newValue) {
      this.debouncedCitizenship(newValue)
    }
  },
  computed: {
    unfilled() {
      return (this.formData.surname === ''
          || this.formData.name === ''
          || this.formData.patronymic === ''
          || this.formData.bDate === ''
          || this.formData.email === ''
          || this.formData.surname === ''
          || this.formData.sex === ''
          || this.formData.cShip === ''
          || this.formData.nPass === ''
          || this.formData.sPass === ''
          || this.formData.dPass === ''
      )
    }
  }
};
</script>

<style scoped>

select {
  width: 250px;
  margin: 15px;
}

button {
  cursor: pointer;
  width: 200px;
  height: 50px;
  margin-top: 20px;
  background-color: blue;
  color: white;
  font-size: 20px;
  border: 5px solid lightskyblue;
  border-radius: 15px;
}

form {
  display: flex;
  flex-direction: column;
}

label {
  color: darkgrey;
  font-size: 24px;
}

li {
  cursor: pointer;
}

input.text {
  border: 3px solid darkgrey;
  height: 30px;
  border-radius: 10px;
  width: 250px;
}

input.radio {
  border: 3px solid darkgrey;
  height: 20px;
  width: 60px;
  cursor: pointer;
}

.row {
  display: flex;
  flex-direction: row;
  justify-content: flex-start;
}
.column {
  display: flex;
  flex-direction: column;
  margin: 15px;
}

.error {
  color: red;
}

.err {
  border-color: red !important;
}
</style>
