<template>
  <validator name="calcValidation">

    <h2>Mifflin-St Jeor</h2>

    <form novalidate>

      <div class="genre-container">
        <label>
          <input type="radio" id="female" name="genre" value="F" v-model="calcData.genre">
          <span>
            <i class="fa fa-female" aria-hidden="true"></i>
          </span>
        </label>

        <label>
          <input type="radio" id="male" name="genre" value="M" v-model="calcData.genre">
          <span>
            <i class="fa fa-male" aria-hidden="true"></i>
          </span>
        </label>
      </div>

      <div class="flex">

        <div class="input-container age-container">
          <label>
            <span class="input-label">
              <i class="fa fa-user" aria-hidden="true"></i> Edad (años)
            </span>
            <button>
              <i class="fa fa-minus" aria-hidden="true"></i>
            </button>
            <input
              autofocus
              autocomplete="off"
              id="age"
              name="age"
              type="tel"
              v-model="calcData.age"
              number
              v-validate:age="rules.age"
              class="form-input"
              v-bind:class="classObject">
            <button>
              <i class="fa fa-plus" aria-hidden="true"></i>
            </button>
          </label>
        </div>

        <div class="input-container weight-container">
          <label>
            <span class="input-label">Peso actual (kg)</span>
            <i class="fa fa-balance-scale" aria-hidden="true"></i>
            <input
              type="number"
              autocomplete="off"
              id="weight"
              name="weight"
              v-model="calcData.weight"
              number
              v-validate:weight="rules.weight">
          </label>
        </div>

      </div>

      <div class="flex">

        <div class="input-container">

          <label>

            <span class="input-label">Altura (cm)</span>
            <i class="fa fa-arrows-v" aria-hidden="true"></i>
            <input
              type="tel"
              id="height"
              name="height"
              v-model="calcData.height"
              number
              v-validate:height="rules.height">

          </label>

        </div>

      </div>

      <hr>

      <hr>

      <span class="input-label">Actividad física</span>

      <select name="activity" id="activity" v-model="calcData.activity">
        <option value="metabolic">Tasa metabólica basal</option>
        <option value="sedentary">Poco / nada de ejercicio</option>
        <option value="3-times">3 veces por semana</option>
        <option value="4-times">4 veces por semana</option>
        <option value="5-times">5 veces por semana</option>
        <option value="dialy">A diario</option>
        <option value="5-times-intense">5 veces por semana (intenso)</option>
        <option value="dialy-intense">Diario (intenso) o dos veces al día</option>
        <option value="dialy-physical-job">Diario + trabajo físico</option>
      </select>

    </form>

    <div v-if="$calcValidation.valid">

      <h2>Resultado</h2>
      {{caloriesInteger}} calorias

      <h4>Macronutrientes</h4>

      <ul>
        <li>Carbohidratos ({{macros.carb.percentage}}%): {{macros.carb.g}}g ({{macros.carb.kcal}} kcal)</li>
        <li>Proteinas ({{macros.protein.percentage}}%): {{macros.protein.g}}g ({{macros.protein.kcal}} kcal)</li>
        <li>Grasas ({{macros.fat.percentage}}%): {{macros.fat.g}}g ({{macros.fat.kcal}} kcal)</li>
      </ul>

    </div>

  </validator>
</template>

<script>
const genreModification = {
  'M': +5,
  'F': -161
}

const activityData = {
  'metabolic': 1.0,
  'sedentary': 1.2,
  '3-times': 1.375,
  '4-times': 1.4187,
  '5-times': 1.4625,
  'dialy': 1.550,
  '5-times-intense': 1.6375,
  'dialy-intense': 1.725,
  'dialy-physical-job': 1.9
}

const calculate = (data) => {
  return (10 * data.weight + 6.25 * data.height - 5 * data.age + genreModification[data.genre]) * activityData[data.activity]
}

const Storage = {
  key: 'calories-calculator',
  set (data) {
    window.localStorage.setItem(this.key, JSON.stringify(data))
  },
  get () {
    return JSON.parse(window.localStorage.getItem(this.key))
  }
}

const defatultCalcData = {
  age: null,
  genre: 'M',
  weight: null,
  height: null,
  activity: 'metabolic'
}

export default {
  data () {
    return {
      calcData: Storage.get() || defatultCalcData,
      rules: {
        age: {
          required: true,
          min: 13,
          max: 80
        },
        weight: {
          required: true,
          min: 35,
          max: 600
        },
        height: {
          required: true,
          min: 50,
          max: 250
        }
      }
    }
  },
  computed: {
    calories () {
      return calculate({
        age: this.calcData.age,
        genre: this.calcData.genre,
        weight: this.calcData.weight,
        height: this.calcData.height,
        activity: this.calcData.activity
      })
    },
    caloriesInteger () {
      return parseInt(this.calories)
    },
    macros () {
      const carbPercentage = 0.45
      const proteinPercentage = 0.30
      const fatPercentage = 0.25

      const carbCalories = this.caloriesInteger * carbPercentage
      const proteinCalories = this.caloriesInteger * proteinPercentage
      const fatCalories = this.caloriesInteger * fatPercentage

      return {
        carb: {
          percentage: carbPercentage * 100,
          g: parseInt(carbCalories / 4),
          kcal: parseInt(carbCalories)
        },
        protein: {
          percentage: proteinPercentage * 100,
          g: parseInt(proteinCalories / 4),
          kcal: parseInt(proteinCalories)
        },
        fat: {
          percentage: fatPercentage * 100,
          g: parseInt(fatCalories / 9),
          kcal: parseInt(fatCalories)
        }
      }
    },
    classObject () {
      return {
        age: true
      }
    }
  },
  watch: {
    calcData: {
      handler: function (data) {
        Storage.set(data)
      },
      deep: true
    }
  }
}
</script>

<style lang="scss" scoped>

  .genre-container {

    display: flex;
    margin-bottom: 20px;

    label {

      display: block;
      width: 50%;
      justify-content: center;
      align-items: center;

      input{
        display: none;
      }

      span{
        border: 3px solid #CACAE6;
        border-radius: 10px 0 0 10px;
        cursor: pointer;
        display: block;
        text-align: center;
        height: 65px;
        display: flex;
        align-items: center;
        justify-content: center;

        .fa{
          font-size: 2.5em;
          color: #CACAE6;
        }

      }

      input:checked + span{
        background: #E9E9F5;
        .fa{
          color: #7272A5;
        }
      }

    }

    label + label{
      span{
        border-left: 0;
        border-radius: 0 10px 10px 0;
      }
    }

  }

  .flex{
    display: flex;
    justify-content: center;
    > div {
      width: 50%;
    }
  }

  .input-container{


    margin-bottom: 20px;
    text-align: center;

    input[type=number]::-webkit-inner-spin-button,
    input[type=number]::-webkit-outer-spin-button {
      -webkit-appearance: none;
      margin: 0;
    }

    input{
      font-size: 2.2em;
      border: 0;
      padding: 0;
      background: #fff;
      width: 80px;
      border: 1px solid #CACAE6;
      border-radius: 2px;
      text-align: center;
      -webkit-appearance: none;
    }

    .input-label{
      display: block;
      margin-bottom: 10px;
    }

    .input-help{
      display: block;
      margin-top: 6px;
    }

  }

</style>

