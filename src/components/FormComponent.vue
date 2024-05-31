<template>
    <div class="contact-form">
        <h2>Vue Contact Form</h2>
        <form @submit.prevent="handleSubmit">
            <div class="form-group">
                <label for="name">Name:</label>
                <input type="text" id="name" v-model="formData.name"
                    :class="v$.formData.name.required === true ? 'is-invalid' : ''" :placeholder="namePlaceholder">
                <span class="form-text text-danger" v-for="error of v$.formData.name.$errors" :key="error.$uid">
                    {{ error.$message }}
                </span>
            </div>
            <div class="form-group">
                <label for="email">Email:</label>
                <input type="email" id="email" v-model="formData.email"
                    :class="v$.formData.name.required === true ? 'is-invalid' : ''" :placeholder="emailPlaceholder">
                <span class="form-text text-danger" v-for="error of v$.formData.email.$errors" :key="error.$uid">
                    {{ error.$message }}
                </span>
            </div>
            <div class="form-group">
                <label for="message">Leave a Message:</label>
                <textarea id="message" v-model="formData.message"
                    :class="{ 'is-invalid': !v$.formData.message.required && !v$.formData.message.$pending }"
                    :placeholder="messagePlaceholder">
                </textarea>
                <span class="form-text text-danger" v-for="error of v$.formData.message.$errors" :key="error.$uid">
                    {{ error.$message }}
                </span>
            </div>
            <button type="submit">Submit</button>
        </form>
        <div class="success-message" v-if="successMessage">
            {{ successMessage }}
        </div>
        <div class="error-message" v-show="errorMessage">
            {{ errorMessage }}
        </div>
    </div>
</template>

<script>
import useVuelidate from '@vuelidate/core';
import { required, email, helpers } from '@vuelidate/validators';
import axios from 'axios';

export default {
    data() {
        return {
            formData: {
                name: '',
                email: '',
                message: ''
            },
            namePlaceholder: 'Enter your full name',
            emailPlaceholder: 'example@accenture.com',
            messagePlaceholder: 'Place your message',
            successMessage: '',
            errorMessage: ''
        };
    },
    validations() {
        return {
            formData: {
                name: {
                    required: helpers.withMessage("Name field is required.", required),
                },
                email: {
                    required: helpers.withMessage("Email field is required.", required),
                    email: helpers.withMessage("Invalid email address.", email)
                },
                message: {
                    required: helpers.withMessage("Message field is required.", required)
                }
            }
        };
    },
    setup() {
        return { v$: useVuelidate() };
    },
    methods: {
        async handleSubmit() {
            this.v$.$touch();
            if (this.v$.$invalid) {
                this.errorMessage = 'Please fix the errors before submitting.';
                return;
            }
            console.log('form', this.formData);

            axios.post('http://localhost:3000/users', this.formData)
                .then(response => {
                    this.successMessage = 'Form submitted successfully!';
                    this.errorMessage = '';
                    // Optionally reset the form
                    this.formData.name = '';
                    this.formData.email = '';
                    this.formData.message = '';
                    setTimeout(() => {
                        this.successMessage = '';
                    }, 3000); // Message disappears after 3 seconds
                    this.v$.$reset();
                })
                .catch(error => {
                    this.errorMessage = 'Error submitting form. Please try again.';
                    this.successMessage = '';
                    console.error('Error:', error);
                });

        }
    }
};
</script>

<style scoped>
.contact-form {
    width: 400px;
    margin: 0 auto;
    padding: 1rem;
    border: 1px solid #ddd;
    border-radius: 5px;
    box-shadow: 2px 2px 10px rgba(0, 0, 0, 0.1);
}

.contact-form h2 {
    text-align: center;
    margin-bottom: 1rem;
}

.form-group {
    margin-bottom: 1rem;
}

.form-group label {
    display: block;
    margin-bottom: 0.5rem;
}

.form-group input,
.form-group textarea {
    width: 100%;
    padding: 0.5rem;
    border: 1px solid #ddd;
    border-radius: 5px;
}

button {
    display: block;
    width: 100%;
    padding: 0.75rem;
    border: none;
    background-color: #28a745;
    color: white;
    border-radius: 5px;
    font-size: 1rem;
    cursor: pointer;
}

button:hover {
    background-color: #218838;
}

.success-message {
    margin-top: 1rem;
    padding: 0.5rem;
    background-color: #d4edda;
    color: #155724;
    border: 1px solid #c3e6cb;
    border-radius: 5px;
}

.error-message {
    margin-top: 1rem;
    padding: 0.5rem;
    background-color: #f8d7da;
    color: #721c24;
    border: 1px solid #f5c6cb;
    border-radius: 5px;
}

.is-invalid {
    border-color: #dc3545;
}
</style>
