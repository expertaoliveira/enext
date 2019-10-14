<template lang='pug'>

    section.form-container
      .form-container__row
        form(method="post" @submit.prevent="cadastrar")
          input(type="text" placeholder="Seu nome completo" name="nome" v-model="nome" required)
          input(type="email" placeholder="Seu email" v-model="email" required)
          input(type="text" placeholder="Seu departamento" v-model="departamento" required)
          input(type="text" placeholder="Seu telefone" v-model="telefone" required)
          input.form-container__row--file(ref="input" type="file" name="image" title="Sua foto" @change="setImage" accept="image/*" required)
          div.image-cropper(v-if="changeImage")
            h2 Defina o corte da imagem
            div.image-cropper__content
              section.__content__area
                div.__img-cropper
                  vue-cropper(ref="cropper" :spect-ratio="16 / 9" :src="imgSrc" preview=".preview")
          button.form-container__row--button Cadastrar

</template>

<script>

  import axios from 'axios'
  import Swal from 'sweetalert2'
  import VueCropper from 'vue-cropperjs';
  import FormData from 'form-data';
  import 'cropperjs/dist/cropper.css';

  export default {
    name: 'Cadastro',
    components: {
      VueCropper,
    },
    data() {
      return {
        nome: '',
        email: '',
        departamento: '',
        telefone: '',
        foto: null,
        data: null,
        imgSrc: '',
        cropImg: '',
        changeImage: false
      }
    },
    methods: {
      cropImage() {
        this.cropImg = this.$refs.cropper.getCroppedCanvas().toDataURL();
        var byteString = atob(this.cropImg.split(',')[1]);
        var ab = new ArrayBuffer(byteString.length);
        var ia = new Uint8Array(ab);

        for (var i = 0; i < byteString.length; i++) {
            ia[i] = byteString.charCodeAt(i);
        }
        this.foto = new Blob([ab], { type: 'image/jpg'});
      },
      setImage(e) {
        this.changeImage = true;
        const file = e.target.files[0];
        if (file.type.indexOf('image/') === -1) {
          alert('Please select an image file');
          return;
        }
        if (typeof FileReader === 'function') {
          const reader = new FileReader();
          reader.onload = (event) => {
            this.imgSrc = event.target.result;
            // rebuild cropperjs with the updated source
            this.$refs.cropper.replace(event.target.result);
          };
          reader.readAsDataURL(file);
        } else {
          alert('Sorry, FileReader API not supported');
        }
      },
      cadastrar() {
        this.cropImage();
        const fd = new FormData();
        fd.set('nome', this.nome);
        fd.set('email', this.email);
        fd.set('departamentoe', this.departamento);
        fd.set('telefone', this.telefone);
        fd.append('foto', this.foto, this.foto.name);

        let config = {
          headers: {
            'Content-Type': 'multipart/form-data' 
          }
        }

        let ObjectRef = this;

        axios.post('http://localhost:3333/cadastro', fd, config)
        .then(response => { 
          ObjectRef.data = response.data;
          Swal.fire({
            html: "<img src='http://localhost:3333/uploads/"+response.data.foto+"' style='width:150px; border-radius: 999px;'>"+
                  "<h2>Obrigado por se cadastrar, <b>"+response.data.nome+"</b></h2>"
          }).then(() => {
            ObjectRef.nome = '';
            ObjectRef.email = '';
            ObjectRef.telefone = '';
            ObjectRef.departamento = '';
            ObjectRef.foto = null;
            ObjectRef.data = null;
            ObjectRef.changeImage = false;
          });
            
        })
        .catch(function (error) {
            ObjectRef.error = error;
        });
      }
    }
  }

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
  
  .form-container__row {
    display: flex;
    flex-direction: column;
    flex-wrap: wrap;
    padding: 10px 7px;
    margin-bottom: 10px;
  }
  .form-container__row input {
    width: 97%;
    padding: 10px 7px;
    margin-bottom: 10px;
  }
  .form-container__row--button {
    margin-top: 30px;
    padding: 15px 7px;
    width: 100%;
    cursor: pointer;
    font-weight: 700;
    background-color: #aaa;
    font-size: 15px;
  }
  .form-container__row--file { 
    border: 1px solid #aaa;
    background-color: #fff;
  }
  .image-cropper__content {
    display: flex;
    justify-content: space-between;
  }
  .__content__area {
    width: 614px;
  }
  .__img-cropper img {
    max-width: 100%;
  }

</style>