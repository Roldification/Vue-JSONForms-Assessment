<template>
  <div>
    <json-forms
      :data="data"
      :renderers="renderers"
      :schema="schema"
      :uischema="uischema"
      :validation-mode="'ValidateAndShow'"
      :additional-errors="errors"
      @change="handleChange($event)"
    >
      
    </json-forms>
    <button :disabled="!isValidAge" type="submit" @click="handleSubmit">Next</button>
  </div>
</template>

<script setup lang="ts">
import { reactive, ref, watch, watchEffect } from 'vue'
import { JsonForms, } from '@jsonforms/vue';



import {
  defaultStyles,
  mergeStyles,
  vanillaRenderers
} from '@jsonforms/vue-vanilla'

const styles = mergeStyles(defaultStyles, {
  control: {
    label: 'control-label',
  }
})

const renderers = [
  ...vanillaRenderers,
]




const schema = {
  properties: {
    firstname: {
      title: 'First Name',
      type: "string",
    },
    lastname: {
      title: 'Last Name',
      type: "string",
    },
    birthday: {
      title: "Birthday",
      type: "string",
      format: "date",
    },
    email: {
      title: "Email",
      type: "string",
    }
  },
};

const uischema = {
  type: "HorizontalLayout",
  elements: [
    {
      type: "VerticalLayout",
      elements: [
        {
          type: "Control",
          scope: "#/properties/firstname",
        },
        {
          type: "Control",
          scope: "#/properties/lastname",
        },
        {
          type: "Control",
          scope: "#/properties/birthday",
        },
        {
          type: "Control",
          scope: "#/properties/email",
        },
      ],
    },
  ],
};

const data = reactive({
  firstname: "",
  lastname: "",
  birthday: '',
  email: '',
})

function handleSubmit() {
  alert('You can now proceed to next step!')
}


// this will run everytime the form changes
function handleChange(event: any) {
  console.log(event)
  data.email = event.data.email
  data.firstname = event.data.firstname
  data.lastname = event.data.lastname
  data.birthday = event.data.birthday
}

type ErrorObject = {
  instancePath: string;
  message: string;
  schemaPath: string;
  keyword: string;
  params: Record<string, unknown>;
};


function countYear(): number {
  try {
    const today = new Date()
    const birthday = new Date(data.birthday)
    const age = today.getFullYear() - birthday.getFullYear()
    const month = today.getMonth() - birthday.getMonth()
    if (month < 0 || (month === 0 && today.getDate() < birthday.getDate())) {
      return age - 1
    }
     return isNaN(age) ? -1 : age
  } catch (error) {
    return -1
  }
}





const errors = ref<ErrorObject[]>([])


watch(
  ()=> data.email,
  (newVal: string, oldVal: string) => {
    console.log(newVal, oldVal)
    if (!newVal.match('^\\S+@\\S+$')) {
      console.log('hellow')
      errors.value.push({
          instancePath: '/email',
          schemaPath: '/properties/email/pattern',
          keyword: 'pattern',
          params: { pattern: '^\\S+@\\S+$' },
          message: 'Invalid Email Format',
        },)
    } else {
      errors.value = errors.value.filter((error) => error.instancePath !== '/email')
    }
  }
)

const isValidAge = ref<boolean>(false)
// ensure that the age is 18 or older everytime birthday is toggled/changed
watch(
  ()=> data.birthday,
  (newVal: string, oldVal: string) => {
    console.log(newVal, oldVal)
    if (countYear() < 18) {
      isValidAge.value = false
      errors.value.push({
        instancePath: '/birthday',
        schemaPath: '/properties/birthday/minimum',
        keyword: 'minimum',
        params: { minimum: 18 },
        message: 'You must be 18 years old or older',
      },)
    } else {
      isValidAge.value = true
      errors.value = errors.value.filter((error) => error.instancePath !== '/birthday')
    }
  }
)




</script>



