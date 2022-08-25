<template>
<div :class="{'has-error': error!=''}">
    <input v-model="rut" class="form-control" name="rut" ref="rut" id="rut" type="text" @blur="validateRut" placeholder="12.345.678-0" required autofocus>
    <div class="help-block alert alert-danger mt-4" v-if="error != ''">
        <strong>{{error}}</strong>
    </div>
<hr>
    <p>Rut: {{rut}}</p>
    <hr>
    <h2>rut sin dv: {{ clearPunto(rutsolito)}}</h2>
</div>
</template>

<script>

export default {
    data() {
        return {
            rut: '',
            error: '',
            rutsolito: '',
        }
    },
    watch: {
        rut: function (val) {
            this.rut = this.cleanRut(this.rut);
            let len = this.rut.length - 1;
            if (len > 3) {
                let dv = this.rut[len];
                let beforeDv = this.rut.substring(0, len).replace(/\B(?=(\d{3})+(?!\d))/g, '.');
                this.rutsolito = beforeDv
                this.rut = beforeDv + '-' + dv;
              
            }
        }
    },
    methods: {
        clearPunto(limpiar){
          return   limpiar.replace(/\./g, '')
        },
        cleanRut(value) {
            return value.replace(/^0+|[^0-9kK]+/g, '').toUpperCase();
        },
        validateRut() {
            let rut = this.cleanRut(this.rut);
            let digito_verificador = this.rut.substr(-1);

            rut = rut.substr(0, rut.length - 1);

            let serie = 0;
            let producto = 0;

            do {

                producto += (rut % 10) * ((serie % 6) + 2);
                serie++;

            } while (rut = Math.floor(rut / 10));

            let resto = producto % 11;
            let resultado = 11 - resto;

            if (resultado == 11) {
                resultado = 0;
            } else if (resultado == 10) {
                resultado = 'K';
            }

            if (digito_verificador != resultado) {
                this.errorInRut();
            } else {
                this.error = '';
            }

        },
        errorInRut() {
            this.error = 'RUT Inválido';
            this.$refs.rut.focus();
        }
    }
}
</script>
