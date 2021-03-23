<template>
  <div id="app">
    <div class="container mx-auto py-16 px-4">
      <h1 class="text-3xl font-bold mb-2">One-time Loan Payment</h1>
      <p>Fill out the form below to complete your payment.</p>
      <form class="my-6" @submit.prevent="onSubmit">
        <div class="border-t mb-4">
          <PaymentText :label="label.loanAccountNumber" v-model="formData.loanAccountNumber" :error="validation.loanAccountNumber" />
          <div class="md:flex">
            <div class="md:w-3/5">
              <div class="border relative border-t-0">
                <div class="absolute font-bold text-xs left-2 top-1">Type of Payment</div>
                <div class="p-2 pt-6">
                  <input type="radio" id="checking" value="checking" v-model="formData.typeOfAccount" @change="clearData()">
                  <label class="ml-1 mr-4" for="checking">Checking</label>
                  <input type="radio" id="debit" value="debit" v-model="formData.typeOfAccount" @change="clearData()">
                  <label class="ml-1 mr-4" for="debit">Debit</label>
                </div>
              </div>
              <template v-if="formData.typeOfAccount === 'checking'">
                <PaymentText :label="label.routingNumber" v-model="formData.routingNumber" :error="validation.routingNumber" />
                <PaymentText :label="label.bankAccountNumber" v-model="formData.bankAccountNumber" :error="validation.bankAccountNumber" />
                <PaymentText :label="`Confirm ${label.confirmBankAccountNumber}`" v-model="formData.confirmBankAccountNumber" :error="validation.confirmBankAccountNumber" />
              </template>
              <template v-if="formData.typeOfAccount === 'debit'">
                <PaymentText :label="label.cardNumber" v-model="formData.cardNumber" :error="validation.cardNumber" />
                <PaymentText :label="label.nameOnCard" v-model="formData.nameOnCard" :error="validation.nameOnCard" />
                <div class="md:flex">
                  <PaymentText :label="label.expDate" v-model="formData.expDate" :error="validation.expDate" />
                  <PaymentText :label="label.cvv" v-model="formData.cvv" :error="validation.cvv" class="md:-ml-px" />
                </div>
              </template>
            </div>
            <div class="bg-gray-50 md:w-2/5 p-2 border border-t-0 md:border-l-0 flex items-center justify-center">
              <img :src="paymentImgSrc" class="mx-auto" />
            </div>
          </div>
        </div>
        <button type="submit" class="btn uppercase font-bold text-white px-8 py-2 md:w-auto w-full">Make Payment</button>
      </form>
    </div>
  </div>
</template>

<script>
import PaymentText from './components/PaymentText'

export default {
  name: 'App',
  components: {
    PaymentText
  },
  data:() => ({
    isFormValid: true,
    label: {
      loanAccountNumber: 'Loan Account Number',
      routingNumber: 'Routing Number',
      bankAccountNumber: 'Bank Account Number',
      confirmBankAccountNumber: 'Bank Account Number',
      cardNumber: 'Card Number',
      nameOnCard: 'Name on Card',
      expDate: 'Expiration Date',
      cvv: 'CVV'
    },
    formData: {
      loanAccountNumber: '',
      typeOfAccount: 'checking',
      routingNumber: '',
      bankAccountNumber: '',
      confirmBankAccountNumber: '',
      cardNumber: '',
      nameOnCard: '',
      expDate: '',
      cvv: ''
    },
    validation: {
      loanAccountNumber: '',
      typeOfAccount: '',
      routingNumber: '',
      bankAccountNumber: '',
      confirmBankAccountNumber: '',
      cardNumber: '',
      nameOnCard: '',
      expDate: '',
      cvv: ''
    }
  }),
  methods: {
    isRequired(array) {
      array.forEach( item => {
        if (!this.formData[item]) {
          this.validation[item] = `${this.label[item]} is required`;
          this.isFormValid = false;
        } else {
          this.validation[item] = '';
        }
      })
    },
    isNumeric(array) {
      const isNumber = (value) => {
        return /^\d+$/.test(value);
      }
      array.forEach( item => {
        if (this.validation[item]) return;
        if (!isNumber(this.formData[item])) {
          this.validation[item] = `${this.label[item]} must be a number`;
          this.isFormValid = false;
        } else {
          this.validation[item] = '';
        }
      })
    },
    customValidation(array) {
      array.forEach( item => {
        if (this.validation[item]) return;
        switch (item) {
          case 'cvv':
            if (this.formData[item].length !== 3) {
              this.validation[item] = 'Value must be 3 digits';
              this.isFormValid = false;
            } else {
              this.validation[item] = '';
            }
            break;
          case 'routingNumber':
            if (this.formData[item].length > 9) {
              this.validation[item] = 'Value must be 9 digits or less';
              this.isFormValid = false;
            } else {
              this.validation[item] = '';
            }
            break;
          case 'bankAccountNumber':
            if (this.formData[item] !== this.formData['confirmBankAccountNumber']) {
              this.validation[item] = 'Bank account numbers do not match';
              this.isFormValid = false;
            } else {
              this.validation[item] = '';
            }
            break;
        }
      })
    },
    validate() {
      if (this.formData.typeOfAccount === 'checking') {
        let requiredArray = ['loanAccountNumber', 'routingNumber', 'bankAccountNumber', 'confirmBankAccountNumber'];
        let numericArray = ['loanAccountNumber', 'routingNumber', 'bankAccountNumber', 'confirmBankAccountNumber'];
        let customArray = ['routingNumber', 'bankAccountNumber'];
        this.isRequired(requiredArray);
        this.isNumeric(numericArray);
        this.customValidation(customArray);
      }
      if (this.formData.typeOfAccount === 'debit') {
        let requiredArray = ['loanAccountNumber', 'cardNumber', 'nameOnCard', 'expDate', 'cvv'];
        let numericArray = ['cardNumber', 'cvv'];
        let customArray = ['cvv'];
        this.isRequired(requiredArray);
        this.isNumeric(numericArray);
        this.customValidation(customArray);
      }
    },
    clearData() {
      Object.keys(this.formData).forEach( key => {
        if (key !== 'loanAccountNumber' && key !== 'typeOfAccount') {
          this.formData[key] = ''
          this.validation[key] = ''
        }
      })
    },
    onSubmit() {
      this.validate();
      if (this.isFormValid) {
        console.log('form submitted');
      }
    },
  },
  computed: {
    paymentImgSrc() {
      return (this.formData.typeOfAccount === 'checking') ? 'img/check.png' : 'img/cvv.png';
    }
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap');

#app {
  font-family: 'Roboto', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
input::placeholder {
  position: absolute;
  top: 0;
  left: 0;
  font-weight: 700;
  font-size: 10px;
}
form {
  max-width: 650px;
}
.btn {
  background-color: #00a39e;
  font-size: 14px;
}
.btn:hover {
  background-color: #02918c;
}


</style>
