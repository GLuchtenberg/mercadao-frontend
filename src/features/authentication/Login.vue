<script>
import { mapActions, mapGetters } from 'vuex';
import { required, minLength, email } from 'vuelidate/lib/validators'
export default {
    methods: {
        ...mapActions('authentication', ['sendLogin','recentlyCreatedAction']),
        submitLogin () {
            this.$v.$touch()
            if (this.$v.form.$invalid) {
                this.$v.$touch()
            } else {
                this.sendLogin(this.form)
                    .then(() => {
                        this.$router.push('/')
                    })
                    .catch(e => {
                        if (e.status == 401) {
                            this.authFails = true;
                            console.log(this.authFails)
                        }
                        console.log('error', e.status)
                    })
            }
        },
        reset () {
            this.form = {
                password: '',
                username: ''
            }
            this.$v.$reset()
        }
    },
    computed: {
        ...mapGetters('authentication',['wasRecentlyCreated'])
    },
    mounted(){
        console.log(this.wasRecentlyCreated)
        if(this.wasRecentlyCreated){
            setTimeout(this.recentlyCreatedAction(false),750)
        }
    },
    data () {
        return {
            form: {
                password: '',
                username: ''
            },
            authFails: false,
        }
    },
    validations: {
        form: {
            username: {
                required,
                minLength: minLength(4),
                email,
            },
            password: {
                required,
                minLength: minLength(4)
            }

        }
    }
}
</script>

<template>
    <section>
        <b-container>
            <b-row>
                <b-col offset="6" cols="6">
                    <b-alert :show="wasRecentlyCreated" variant="success">
                        Successfully registered.
                        Welcome to mercadão. :D
                    </b-alert>
                    <b-alert :show="authFails" variant="danger">
                        E-mail or password incorrects.
                    </b-alert>
                    <b-form @submit.prevent="submitLogin">
                        <div :class=" { invalid: $v.form.username.$dirty && $v.form.username.$invalid } ">
                            <label for="email" clasl="sr-only">E-mail</label>
                            <b-input id="email" name="email" placeholder="email" v-model="form.username" @input="$v.form.username.$touch()" />
                        </div>
                        <div :class=" { invalid: $v.form.password.$dirty && $v.form.password.$invalid } ">
                            <label for="password" clasl="sr-only">Senha</label>
                            <b-input id="password" type="password" name="password" placeholder="senha" v-model="form.password" @input="$v.form.password.$touch()" />
                        </div>
                        <button type="submit" class="btn btn-primary">Entrar</button>
                    </b-form>
                </b-col>
            </b-row>
        </b-container>
    </section>
</template>

