<template>
  <div id="app">
    <div v-if="inicio && !asignaturas && !naturales && !juego && !felicidades" class="home">
      <img :src="require('@/assets/logo.jpg')">
      <button @click="goAsignaturas" style="margin-top: 50px;">¡COMENCEM!</button>
    </div>
    <div v-if="!inicio && asignaturas && !naturales && !juego" class="lista-asignaturas">
      <img :src="require('@/assets/logo.jpg')">
      <button @click="goNaturales" class="asignaturas naturales" style="margin-top: 50px;">
        CIÈNCIES NATURALS
      </button>
      <button class="asignaturas sociales">
        CIÈNCIES SOCIALS
        <img :src="require('@/assets/candado.svg')">
      </button>
      <button class="asignaturas mates">
        MATEMÁTIQUES
        <img :src="require('@/assets/candado.svg')">
      </button>
      <button class="asignaturas caste">
        LLENGUA CASTELLANA
        <img :src="require('@/assets/candado.svg')">
      </button>
      <button class="asignaturas ingles">
        INGLÉS
        <img :src="require('@/assets/candado.svg')">
      </button>
    </div>
    <div
      v-if="!inicio && !asignaturas && naturales && !juego && !felicidades"
      class="tema-naturales"
    >
      <button @click="goNaturales" class="asignaturas naturales" style="diplay: inline-block;">
        CIÈNCIES NATURALS
      </button>
      <p style="color: #538135" @click="cambioTema(1)">
        1. EL NOSTRE COS
      </p>
      <p style="color: #4b7430" @click="cambioTema(2)">
        2. VISCA LA VIDA SALUDABLE!
      </p>
      <p style="color: #42672a" @click="cambioTema(3)">
        3. OBSERVEM ELS ÉSSERS VIUS
      </p>
      <p style="color: #3a5a25" @click="cambioTema(4)">
        4. LA NATURALESA
      </p>
      <p style="color: #324d20" @click="cambioTema(5)">
        5. AMB MATERIALS I AMB ENERGIA!
      </p>
      <p style="color: #2a411b" @click="cambioTema(6)">
        6. QUINA MÀQUINA!
      </p>
    </div>
    <div v-if="!inicio && !asignaturas && !naturales && juego && !felicidades" class="juego">
      <button class="asignaturas naturales">{{titulo}}</button>
      <div v-if="solucion.length" class="solucion">
        <p>Solució: {{this.solucion}}</p>
      </div>
      <img
        v-if="textoJuego.includes('?') || solucion.length"
        :src="require(`@/assets/${imagen}`)"
        class="img-solucion"
      >
      <p v-if="textoJuego" class="texto-juego">{{textoJuego}}</p>
      <div v-if="tiempoEjercicio < 1" class="pista-bloque">
        <div v-if="numeroPista.length" class="pista-texto">
          <div class="titulo">{{numeroPista}}</div>
          <div class="texto">{{pistaTexto}}</div>
        </div>
        <b-progress
          v-if="!solucion.length"
          :value="3 - tiempoPista"
          :max="3"
          variant="success"
          show-progress
        >
          <b-progress-bar :value="3 - tiempoPista">
            <span><strong>{{tiempoPista}}</strong></span>
          </b-progress-bar>
        </b-progress>
      </div>
      <div v-if="tiempoEjercicio > 0 && !solucion.length  && pistaTexto.length" class="counter">
        <b-progress
          :max="3"
          variant="success"
          show-progress
        >
          <b-progress-bar class="progress-bar" :value="3 - tiempoEjercicio">
            <span><strong>{{tiempoEjercicio}}</strong></span>
          </b-progress-bar>
        </b-progress>
      </div>
      <p>{{mensaje}}</p>
      <b-progress
        v-if="solucion.length"
        :max="20"
        variant="success"
        show-progress
        >
          <b-progress-bar class="progress-bar" :value="20 - tiempoSolucion">
            <span><strong>{{tiempoSolucion}}</strong></span>
          </b-progress-bar>
        </b-progress>
    </div>
    <div v-if="!inicio && !asignaturas && !naturales && !juego && felicidades" class="felicidades">
      <img :src="require(`@/assets/felicidades.jpg`)">
      <button
        @click="goNaturales"
        class="asignaturas naturales"
        style="diplay:inline-block;
        width:auto;
        font-size:15px;
        min-height:auto;
        float:right;
        margin:15px;"
      >
        MENÚ
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      asignaturas: false,
      naturales: false,
      inicio: true,
      juego: false,
      felicidades: false,
      titulo: '',
      textoJuego: '',
      tiempoEjercicio: 3,
      tiempoPista: 3,
      tiempoSolucion: 0,
      contadorTema: 1,
      contadorJuego: 1,
      contadorFaseJuego: 1,
      numeroPista: 'Pista 1',
      pistaTexto: '',
      solucion: '',
      textoSolucion: '',
      imagen: 'lupa.jpg',
      mensaje: '',
    };
  },
  watch: {
    textoJuego() {
      this.tiempoEjercicio = this.textoJuego.includes('?') ? 1 : 3;
      const contadorInterval = setInterval(() => {
        this.tiempoEjercicio -= 1;
        if (this.tiempoEjercicio === 0 || this.solucion.length) {
          clearInterval(contadorInterval);
        }
      }, 1000);
    },
    tiempoEjercicio(valor) {
      if (valor < 1) {
        this.tiempoPista = 3;
        const contadorPistaInterval = setInterval(() => {
          this.tiempoPista -= 1;
          if (this.tiempoPista === 0 || this.solucion.length) {
            clearInterval(contadorPistaInterval);
          }
        }, 1000);
      }
    },
    tiempoPista(valor) {
      if (valor < 1) {
        this.contadorFaseJuego += 1;
        this[this.cambioJuegoTemas[this.contadorTema]](this.contadorJuego, this.contadorFaseJuego);
      }
    },
  },
  computed: {
    cambioJuegoTemas() {
      return [
        '',
        'cambioJuegoCos',
        'cambioJuegoVida',
        'cambioJuegoEsser',
        'cambioJuegoNatural',
        'cambioJuegoMaterial',
        'cambioJuegoMaquina',
      ];
    },
  },
  methods: {
    goAsignaturas() {
      this.asignaturas = true;
      this.inicio = false;
    },
    goNaturales() {
      this.naturales = true;
      this.juego = false;
      this.asignaturas = false;
      this.felicidades = false;
    },
    iniciarTiempoSolucion() {
      this.tiempoSolucion = 20;
      const contadorSolucionInterval = setInterval(() => {
        this.tiempoSolucion -= 1;
        if (this.tiempoSolucion === 0) {
          this.siguienteJuego();
          clearInterval(contadorSolucionInterval);
        }
      }, 1000);
    },
    cambioTema(numero) {
      switch (numero) {
        case 1:
          this.cambioJuegoCos();
          break;
        case 2:
          this.cambioJuegoVida();
          break;
        case 3:
          this.cambioJuegoEsser();
          break;
        case 4:
          this.cambioJuegoNatural();
          break;
        case 5:
          this.cambioJuegoMaterial();
          break;
        case 6:
          this.cambioJuegoMaquina();
          break;
        default:
          break;
      }
    },

    cambioJuegoCos(numero = 1, faseJuego = 1) {
      this.contadorTema = 1;
      this.titulo = '1. EL NOSTRE COS';
      this.contadorJuego = numero;
      this.contadorFaseJuego = faseJuego;
      this.juego = true;
      this.naturales = false;

      switch (numero) {
        case 1:
          this.juegoCos1(faseJuego);
          break;
        case 2:
          this.juegoCos2(faseJuego);
          break;
        case 3:
          this.juegoCos3(faseJuego);
          break;
        default:
          this.showFelicidades();
          break;
      }
    },
    cambioJuegoVida(numero = 1, faseJuego = 1) {
      this.contadorTema = 2;
      this.titulo = '2. VISCA LA VIDA SALUDABLE';
      this.contadorJuego = numero;
      this.contadorFaseJuego = faseJuego;
      this.juego = true;
      this.naturales = false;

      switch (numero) {
        case 1:
          this.juegoVida1(faseJuego);
          break;
        case 2:
          this.juegoVida2(faseJuego);
          break;
        case 3:
          this.juegoVida3(faseJuego);
          break;
        default:
          this.showFelicidades();
          break;
      }
    },
    cambioJuegoEsser(numero = 1, faseJuego = 1) {
      this.contadorTema = 3;
      this.titulo = '3. OBSERVEM ELS ÉSSERS VIUS';
      this.contadorJuego = numero;
      this.contadorFaseJuego = faseJuego;
      this.juego = true;
      this.naturales = false;

      switch (numero) {
        case 1:
          this.juegoEsser1(faseJuego);
          break;
        case 2:
          this.juegoEsser2(faseJuego);
          break;
        default:
          this.showFelicidades();
          break;
      }
    },
    cambioJuegoNatural(numero = 1, faseJuego = 1) {
      this.contadorTema = 4;
      this.titulo = '4. LA NATURALESSA';
      this.contadorJuego = numero;
      this.contadorFaseJuego = faseJuego;
      this.juego = true;
      this.naturales = false;

      switch (numero) {
        case 1:
          this.juegoNatural1(faseJuego);
          break;
        case 2:
          this.juegoNatural2(faseJuego);
          break;
        case 3:
          this.juegoNatural3(faseJuego);
          break;
        default:
          this.showFelicidades();
          break;
      }
    },
    cambioJuegoMaterial(numero = 1, faseJuego = 1) {
      this.contadorTema = 5;
      this.titulo = '5. AMB MATERIALS I AMB ENERGIA!';
      this.contadorJuego = numero;
      this.contadorFaseJuego = faseJuego;
      this.juego = true;
      this.naturales = false;

      switch (numero) {
        case 1:
          this.juegoMaterial1(faseJuego);
          break;
        case 2:
          this.juegoMaterial2(faseJuego);
          break;
        case 3:
          this.juegoMaterial3(faseJuego);
          break;
        default:
          this.showFelicidades();
          break;
      }
    },
    cambioJuegoMaquina(numero = 1, faseJuego = 1) {
      this.contadorTema = 6;
      this.titulo = '6. QUINA MÁQUINA!';
      this.contadorJuego = numero;
      this.contadorFaseJuego = faseJuego;
      this.juego = true;
      this.naturales = false;

      switch (numero) {
        case 1:
          this.juegoMaquina1(faseJuego);
          break;
        case 2:
          this.juegoMaquina2(faseJuego);
          break;
        case 3:
          this.juegoMaquina3(faseJuego);
          break;
        default:
          this.showFelicidades();
          break;
      }
    },
    showFelicidades() {
      this.juego = false;
      this.felicidades = true;
      this.contadorFaseJuego = 1;
    },
    siguienteJuego() {
      this.solucion = '';
      this.numeroPista = '';
      this.imagen = 'lupa.jpg';
      this.mensaje = '';
      this.contadorFaseJuego = 1;
      this.contadorJuego += 1;
      this[this.cambioJuegoTemas[this.contadorTema]](this.contadorJuego, this.contadorFaseJuego);
    },
    juegoCos1(faseJuego) {
      switch (faseJuego) {
        case 1:
          this.textoJuego = "HAS D´ANAR A LA CADIRA MÉS PROPERA. SEU I ALÇA'T TOTES LES VEGADES QUE PUGUES";
          this.pistaTexto = 'ÉS UNA PART DEL COS';
          break;
        case 2:
          this.textoJuego = "AGAFA 2 LLIBRES (CADA UN EN UNA MÀ), POSA'T A PEU COIX I MANTINGUES L'EQUILIBRI.";
          this.numeroPista = 'Pista 2';
          this.pistaTexto = 'ESTÀ PER TOT EL COS';
          break;
        case 3:
          this.textoJuego = "CERCA UNA BOTELLA D'AIGUA I AGAFA-LA I DEIXA-LA EN EL SÒL TANTES VEGADES COM PUGUES.";
          this.numeroPista = 'Pista 3';
          this.pistaTexto = 'AMB ELLA S´UTILITZA EL SENTIT DEL TACTE';
          break;
        case 4:
          this.textoJuego = 'IMPROVISA! PENSA EN UNA CANÇÓ I BALLA-LA COM TU VULGUES';
          this.numeroPista = 'Pista 4';
          this.pistaTexto = 'AMB ELLA SENTIM EL QUE TOQUEM';
          break;
        case 5:
          this.textoJuego = 'QUINA PART DEL COS ÉS?';
          this.pistaTexto = '';
          this.numeroPista = '';
          break;
        case 6:
          this.textoJuego = '';
          this.solucion = 'LA PELL';
          this.numeroPista = '';
          this.imagen = 'piel.jpg';
          this.mensaje = '¡RESPIREM!';
          this.iniciarTiempoSolucion();
          break;
        default:
          break;
      }
    },
    juegoCos2(faseJuego) {
      switch (faseJuego) {
        case 1:
          this.textoJuego = "AGAFA EL PRIMER QUE TINGUES A MÀ (QUE NO ES PUGA TRENCAR I QUE NO PESE MOLT) PER EXEMPLE: UNS CALCETINS I PORTA-LOS D'UN EXTREM A UN ALTRE DEL TEU SALÓ";
          this.numeroPista = 'Pista 1';
          this.pistaTexto = 'ES TROBA DINS DEL COS';
          break;
        case 2:
          this.textoJuego = 'PUJA I BAIXA ELS TEUS BRAÇOS TANTES VEGADES COM PUGUES.';
          this.numeroPista = 'Pista 2';
          this.pistaTexto = 'ÉS UN ÒRGAN';
          break;
        case 3:
          this.textoJuego = "IMAGINA QUE SALTES AMB LA CORDA, DÓNA TOTS ELS SALTS POSSIBLES SENSE MOURE'T DEL LLOC.";
          this.numeroPista = 'Pista 3';
          this.pistaTexto = 'ES TROBA PER LA PART DEL PIT';
          break;
        case 4:
          this.textoJuego = "TOMBA'T A TERRA CAP AMUNT I OBRI I TANCA LES CAMES TANTES VEGADES COM PUGUES.";
          this.numeroPista = 'Pista 4';
          this.pistaTexto = 'MOU LA SANG QUE CIRCULA PEL COS';
          break;
        case 5:
          this.textoJuego = 'QUINA PART DEL COS ÉS?';
          this.pistaTexto = '';
          this.numeroPista = '';
          break;
        case 6:
          this.textoJuego = '';
          this.solucion = 'EL COR';
          this.numeroPista = '';
          this.imagen = 'cor.jpg';
          this.mensaje = '¡RESPIREM!';
          this.iniciarTiempoSolucion();
          break;
        default:
          break;
      }
    },
    juegoCos3(faseJuego) {
      switch (faseJuego) {
        case 1:
          this.textoJuego = "TOMBA'T A TERRA CAP AVALL I MOU ELS BRAÇOS COM SI ESTIGUERES NADANT.";
          this.numeroPista = 'Pista 1';
          this.pistaTexto = 'ÉS UNA ARTICULACIÓ';
          break;
        case 2:
          this.textoJuego = 'ACATXA´T I ALÇA´T TANTES VEGADES COM PUGUES.';
          this.numeroPista = 'Pista 2';
          this.pistaTexto = 'COMENÇA PER LA LLETRA “M”';
          break;
        case 3:
          this.textoJuego = "EN EL TEU CORREDOR, CORRE DONANT PASSOS MOLT GRANS FINS QUE S'ACABE EL TEMPS (ANADA I TORNADA).";
          this.numeroPista = 'Pista 3';
          this.pistaTexto = 'UNEIX EL BRAÇ AMB LA MÀ';
          break;
        case 4:
          this.textoJuego = "TOMBA'T A TERRA CAP AMUNT, FLEXIONA ELS GENOLLS I INTENTA TOCAR-LES AMB LES TEUES MANS.";
          this.numeroPista = 'Pista 4';
          this.pistaTexto = 'GRÀCIES A ELLA PODEM SALUDAR';
          break;
        case 5:
          this.textoJuego = 'QUINA PART DEL COS ÉS?';
          this.pistaTexto = '';
          this.numeroPista = '';
          break;
        case 6:
          this.textoJuego = '';
          this.solucion = 'LA MONYICA';
          this.numeroPista = '';
          this.imagen = 'monyica.jpg';
          this.mensaje = '¡RESPIREM!';
          this.iniciarTiempoSolucion();
          break;
        default:
          break;
      }
    },
    juegoVida1(faseJuego) {
      switch (faseJuego) {
        case 1:
          this.textoJuego = "PASSA A QUATRE POTES PER DAVALL D'UNA TAULA I CADA VEGADA QUE ISQUES FES UN SALT. FES-HO TANTES VEGADES COM PUGUES FINS QUE S'ACABE EL TEMPS";
          this.numeroPista = 'Pista 1';
          this.pistaTexto = 'ÉS PART DE LA NOSTRA DIETA';
          break;
        case 2:
          this.textoJuego = 'EL SÒL ÉS LAVA! CORRE EN EL LLOC PER A NO CREMAR-TE!';
          this.numeroPista = 'Pista 2';
          this.pistaTexto = 'ÉS NECESSÀRIA PER A TINDRE UNA VIDA SALUDABLE';
          break;
        case 3:
          this.textoJuego = "DES D'ON ESTIGUES FENT LES ACTIVITATS HAS D´ANAR FINS A LA PORTA DE CASA TOTES LES VEGADES QUE PUGUES.";
          this.numeroPista = 'Pista 3';
          this.pistaTexto = 'ES PREN EN ELS CINC MENJARS DEL DIA';
          break;
        case 4:
          this.textoJuego = 'SALTA TOT EL QUE PUGUES A PEU COIX';
          this.numeroPista = 'Pista 4';
          this.pistaTexto = 'TAMBÉ LA BEVEM QUAN FEM ESPORT I TENIM CALOR';
          break;
        case 5:
          this.textoJuego = 'QUÈ ÉS?';
          this.pistaTexto = '';
          this.numeroPista = '';
          break;
        case 6:
          this.textoJuego = '';
          this.solucion = "L'AIGUA";
          this.numeroPista = '';
          this.imagen = 'aigua.jpg';
          this.mensaje = '¡RESPIREM!';
          this.iniciarTiempoSolucion();
          break;
        default:
          break;
      }
    },
    juegoVida2(faseJuego) {
      switch (faseJuego) {
        case 1:
          this.textoJuego = 'TOMBA´T I ALÇA´T DEL SÒL TOTES LES VEGADES POSSIBLES.';
          this.numeroPista = 'Pista 1';
          this.pistaTexto = 'ÉS UN ESPORT';
          break;
        case 2:
          this.textoJuego = "TOMBA'T I ALÇA ELS BRAÇOS TOTES LES VEGADES QUE PUGUES.";
          this.numeroPista = 'Pista 2';
          this.pistaTexto = 'S´UTILITZA UNA PILOTA';
          break;
        case 3:
          this.textoJuego = 'CANTA UNA CANÇÓ MENTRE VAS DE LA TEUA HABITACIÓ A LA SALA TOTES LES VEGADES POSSIBLES.';
          this.numeroPista = 'Pista 3';
          this.pistaTexto = 'ES JUGA EN PARELLES';
          break;
        case 4:
          this.textoJuego = "DEMOSTRA QUE ETS LA XICA O EL XIC MÉS FORT DE LA TEUA CLASSE I AGAFA TANTS LLIBRES COM PUGUES. ALÇA'LS I TORNA A DEIXAR-LOS A TERRA TANTES VEGADES COM PUGUES.";
          this.numeroPista = 'Pista 4';
          this.pistaTexto = 'S´UTILITZA UNA RAQUETA';
          break;
        case 5:
          this.textoJuego = 'QUÈ ESPORT ÉS?';
          this.pistaTexto = '';
          this.numeroPista = '';
          break;
        case 6:
          this.textoJuego = '';
          this.solucion = 'TENNIS';
          this.numeroPista = '';
          this.imagen = 'tennis.jpg';
          this.mensaje = '¡RESPIREM!';
          this.iniciarTiempoSolucion();
          break;
        default:
          break;
      }
    },
    juegoVida3(faseJuego) {
      switch (faseJuego) {
        case 1:
          this.textoJuego = 'ETS EL O LA MÉS VELOÇ DE LA CLASSE? DÓNA TANTES VOLTES COM PUGUES AL TEU CORREDOR.';
          this.numeroPista = 'Pista 1';
          this.pistaTexto = 'ÉS NECESSARI PER A TINDRE UNA BONA SALUT';
          break;
        case 2:
          this.textoJuego = "CORRE! AGAFA UNS CALCETINS I LLANÇA´LS TAN LLUNY COM PUGUES, VÉS A PER ELLS I REPETEIX L'ACCIÓ TOTES LES VEGADES POSSIBLES.";
          this.numeroPista = 'Pista 2';
          this.pistaTexto = 'AMB ELL ENS NETEGEM UNA PART DEL NOSTRE COS';
          break;
        case 3:
          this.textoJuego = "BALLA AL RITME DE LA MÚSICA, LA PERSONA QUE ESTIGA A PROP TEU HAURÀ DE CANTAR UNA CANÇÓ I TU HAURÀS DE BALLAR A RITME D'ELLA.";
          this.numeroPista = 'Pista 3';
          this.pistaTexto = 'HO UTILITZEM DESPRÉS DE CADA MENJAR';
          break;
        case 4:
          this.textoJuego = "TOMBA'T CAP AMUNT, POSA LES MANS AL TEU CULET I ALÇA LES CAMES ESTIRADES TANTES VEGADES COM PUGUES.";
          this.numeroPista = 'Pista 4';
          this.pistaTexto = 'ES POT UTILITZAR AMB PASTA DE DENTS';
          break;
        case 5:
          this.textoJuego = 'QUÈ ÉS?';
          this.pistaTexto = '';
          this.numeroPista = '';
          break;
        case 6:
          this.textoJuego = '';
          this.solucion = 'RASPALL DE DENTS';
          this.numeroPista = '';
          this.imagen = 'raspall.jpg';
          this.mensaje = '¡RESPIREM!';
          this.iniciarTiempoSolucion();
          break;
        default:
          break;
      }
    },
    juegoEsser1(faseJuego) {
      switch (faseJuego) {
        case 1:
          this.textoJuego = "FES COM SI ESTIGUESSIS ASSEGUT EN L'AIRE I TOCA LA PARET AMB LA ESQUENA.";
          this.numeroPista = 'Pista 1';
          this.pistaTexto = 'ÉS UN ÉSSER VIU';
          break;
        case 2:
          this.textoJuego = "CREA UN PONT AMB EL TEU COS. TOMBA'T CAP AVALL RECOLZANT NOMÉS ELS PEUS I LES MANS";
          this.numeroPista = 'Pista 2';
          this.pistaTexto = 'ÉS UN ANIMAL';
          break;
        case 3:
          this.textoJuego = 'CORRE AL LLOC INTENTANT PORTAR ELS TEUS GENOLLS A EL PIT';
          this.numeroPista = 'Pista 3';
          this.pistaTexto = 'ÉS UN ANIMAL VERTEBRAT';
          break;
        case 4:
          this.textoJuego = 'SEU A TERRA I TOCA LA PUNTA DELS TEUS PEUS AMB LA PUNTA DELS DITS DE LA MÀ.';
          this.numeroPista = 'Pista 4';
          this.pistaTexto = 'ÉS UN MAMÍFER';
          break;
        case 5:
          this.textoJuego = "RÀPID! CREA AL TEU CAP UN RECORREGUT PER LA TEUA CASA I FES-HO ABANS QUE S'ACABE EL TEMPS.";
          this.numeroPista = 'Pista 5';
          this.pistaTexto = 'ÉS HERBÍVOR';
          break;
        case 6:
          this.textoJuego = 'CORRE EN EL LLOC MOVENT CAMES I BRAÇOS.';
          this.numeroPista = 'Pista 6';
          this.pistaTexto = 'ÉS GRAN, TÉ TROMPA I VIU A LA SELVA';
          break;
        case 7:
          this.textoJuego = 'QUÈ ÉSSER VIU ÉS?';
          this.pistaTexto = '';
          this.numeroPista = '';
          break;
        case 8:
          this.textoJuego = '';
          this.solucion = "L'ELEFANT";
          this.numeroPista = '';
          this.imagen = 'elefant.jpg';
          this.mensaje = '¡RESPIREM!';
          this.iniciarTiempoSolucion();
          break;
        default:
          break;
      }
    },
    juegoEsser2(faseJuego) {
      switch (faseJuego) {
        case 1:
          this.textoJuego = "HAS D´ANAR A LA CADIRA MÉS PROPERA. SEU I ALÇA'T TOTES LES VEGADES QUE PUGUES";
          this.numeroPista = 'Pista 1';
          this.pistaTexto = 'ÉS UN ÉSSER VIU';
          break;
        case 2:
          this.textoJuego = "AGAFA 2 LLIBRES (CADA UN EN UNA MÀ), POSA'T A PEU COIX I MANTINGUES L'EQUILIBRI.";
          this.numeroPista = 'Pista 2';
          this.pistaTexto = 'ÉS UNA PLANTA';
          break;
        case 3:
          this.textoJuego = "CERCA UNA BOTELLA D'AIGUA I AGAFA-LA I DEIXA-LA EN EL SÒL TANTES VEGADES COM PUGUES.";
          this.numeroPista = 'Pista 3';
          this.pistaTexto = 'ÉS GRAN, DE TIJA AMPLA I DURA';
          break;
        case 4:
          this.textoJuego = 'IMPROVISA! PENSA EN UNA CANÇÓ I BALLA-LA COM TU VULGUES.';
          this.numeroPista = 'Pista 4';
          this.pistaTexto = 'NO TÉ FLORS';
          break;
        case 5:
          this.textoJuego = "AGAFA EL PRIMER QUE TINGUES A MÀ (QUE NO ES PUGA TRENCAR I QUE NO PESE MOLT) PER EXEMPLE: UNS CALCETINS I PORTA-LOS D'UN EXTREM A UN ALTRE DEL TEU SALÓ";
          this.numeroPista = 'Pista 5';
          this.pistaTexto = 'SÍ TÉ FRUITS';
          break;
        case 6:
          this.textoJuego = 'PUJA I BAIXA ELS TEUS BRAÇOS TANTES VEGADES COM PUGUES.';
          this.numeroPista = 'Pista 6';
          this.pistaTexto = 'TÉ PINYES I COMEÇA PER LA LLETRA “P”';
          break;
        case 7:
          this.textoJuego = 'QUÈ ÉSSER VIU ÉS?';
          this.pistaTexto = '';
          this.numeroPista = '';
          break;
        case 8:
          this.textoJuego = '';
          this.solucion = 'PI';
          this.numeroPista = '';
          this.imagen = 'pi.jpg';
          this.mensaje = '¡RESPIREM!';
          this.iniciarTiempoSolucion();
          break;
        default:
          break;
      }
    },
    juegoNatural1(faseJuego) {
      switch (faseJuego) {
        case 1:
          this.textoJuego = "IMAGINA QUE SALTES AMB LA CORDA, DÓNA TOTS ELS SALTS POSSIBLES SENSE MOURE'T DEL LLOC.";
          this.numeroPista = 'Pista 1';
          this.pistaTexto = 'ÉS UN ANIMAL SALVATGE';
          break;
        case 2:
          this.textoJuego = "TOMBA'T A TERRA CAP AMUNT I OBRI I TANCA LES CAMES TANTES VEGADES COM PUGUES.";
          this.numeroPista = 'Pista 2';
          this.pistaTexto = 'TÉ ALES I VOLA';
          break;
        case 3:
          this.textoJuego = "TOMBA'T A TERRA CAP AVALL I MOU ELS BRAÇOS COM SI ESTIGUERES NADANT.";
          this.numeroPista = 'Pista 3';
          this.pistaTexto = 'VIUEN EN RUSCOS';
          break;
        case 4:
          this.textoJuego = 'ACATXA´T I ALÇA´T TANTES VEGADES COM PUGUES.';
          this.numeroPista = 'Pista 4';
          this.pistaTexto = 'FABRICA LA MEL';
          break;
        case 5:
          this.textoJuego = 'QUÈ ANIMAL ÉS?';
          this.pistaTexto = '';
          this.numeroPista = '';
          break;
        case 6:
          this.textoJuego = '';
          this.solucion = "L'ABELLA";
          this.numeroPista = '';
          this.imagen = 'abella.jpg';
          this.mensaje = '¡RESPIREM!';
          this.iniciarTiempoSolucion();
          break;
        default:
          break;
      }
    },
    juegoNatural2(faseJuego) {
      switch (faseJuego) {
        case 1:
          this.textoJuego = "EN EL TEU CORREDOR, CORRE DONANT PASSOS MOLT GRANS FINS QUE S'ACABE EL TEMPS (ANADA I TORNADA).";
          this.numeroPista = 'Pista 1';
          this.pistaTexto = 'S´OBTÉ DE LES PLANTES';
          break;
        case 2:
          this.textoJuego = "TOMBA'T A TERRA CAP AMUNT, FLEXIONA ELS GENOLLS I INTENTA TOCAR-LES AMB LES TEUES MANS.";
          this.numeroPista = 'Pista 2';
          this.pistaTexto = 'SERVEIX PER FER ROBA';
          break;
        case 3:
          this.textoJuego = "PASSA A QUATRE POTES PER DAVALL D'UNA TAULA I CADA VEGADA QUE ISQUES FES UN SALT. FES-HO TANTES VEGADES COM PUGUES FINS QUE S'ACABE EL TEMPS";
          this.numeroPista = 'Pista 3';
          this.pistaTexto = 'ÉS DE COLR BLANC';
          break;
        case 4:
          this.textoJuego = 'EL SÒL ÉS LAVA! CORRE EN EL LLOC PER A NO CREMAR-TE!';
          this.numeroPista = 'Pista 4';
          this.pistaTexto = 'COMENÇA PER LA LLETRA “C”';
          break;
        case 5:
          this.textoJuego = 'QUÈ ÉS?';
          this.pistaTexto = '';
          this.numeroPista = '';
          break;
        case 6:
          this.textoJuego = '';
          this.solucion = 'EL COTÓ';
          this.numeroPista = '';
          this.imagen = 'coto.jpg';
          this.mensaje = '¡RESPIREM!';
          this.iniciarTiempoSolucion();
          break;
        default:
          break;
      }
    },
    juegoNatural3(faseJuego) {
      switch (faseJuego) {
        case 1:
          this.textoJuego = "DES D'ON ESTIGUES FENT LES ACTIVITATS HAS D´ANAR FINS A LA PORTA DE CASA TOTES LES VEGADES QUE PUGUES.";
          this.numeroPista = 'Pista 1';
          this.pistaTexto = 'ÉS UNA ACCIÓ MOLT IMPORTANT';
          break;
        case 2:
          this.textoJuego = 'SALTA TOT EL QUE PUGUES A PEU COIX';
          this.numeroPista = 'Pista 2';
          this.pistaTexto = 'SERVEIX PER CUIDAR EL MEDI AMBIENT';
          break;
        case 3:
          this.textoJuego = 'TOMBA´T I ALÇA´T DEL SÒL TOTES LES VEGADES POSSIBLES.';
          this.numeroPista = 'Pista 3';
          this.pistaTexto = 'GRÀCIES A AQUESTA ACCIÓ ES PODEN REUTILITZAR OBJECTES';
          break;
        case 4:
          this.textoJuego = "TOMBA'T I ALÇA ELS BRAÇOS TOTES LES VEGADES QUE PUGUES.";
          this.numeroPista = 'Pista 4';
          this.pistaTexto = 'PER FER AQUESTA ACCIÓ CAL TIRAR EL FEM A CONTENIDORS DE DIFERENTS COLORS';
          break;
        case 5:
          this.textoJuego = 'QUÈ ACCIÓ ÉS?';
          this.pistaTexto = '';
          this.numeroPista = '';
          break;
        case 6:
          this.textoJuego = '';
          this.solucion = 'RECICLAR';
          this.numeroPista = '';
          this.imagen = 'reciclar.jpg';
          this.mensaje = '¡RESPIREM!';
          this.iniciarTiempoSolucion();
          break;
        default:
          break;
      }
    },
    juegoMaterial1(faseJuego) {
      switch (faseJuego) {
        case 1:
          this.textoJuego = 'CANTA UNA CANÇÓ MENTRE VAS DE LA TEUA HABITACIÓ A LA SALA TOTES LES VEGADES POSSIBLES.';
          this.numeroPista = 'Pista 1';
          this.pistaTexto = 'ÉS UN OBJECTE';
          break;
        case 2:
          this.textoJuego = "DEMOSTRA QUE ETS LA XICA O EL XIC MÉS FORT DE LA TEUA CLASSE I AGAFA TANTS LLIBRES COM PUGUES. ALÇA'LS I TORNA A DEIXAR-LOS A TERRA TANTES VEGADES COM PUGUES.";
          this.numeroPista = 'Pista 2';
          this.pistaTexto = 'ESTÀ FET AMB UN MATERIAL NATURAL: FUSTA';
          break;
        case 3:
          this.textoJuego = 'ETS EL O LA MÉS VELOÇ DE LA CLASSE? DÓNA TANTES VOLTES COM PUGUES AL TEU CORREDOR.';
          this.numeroPista = 'Pista 3';
          this.pistaTexto = 'ES DURA I RÍGIDA';
          break;
        case 4:
          this.textoJuego = "CORRE! AGAFA UNS CALCETINS I LLANÇA´LS TAN LLUNY COM PUGUES, VÉS A PER ELLS I REPETEIX L'ACCIÓ TOTES LES VEGADES POSSIBLES.";
          this.numeroPista = 'Pista 4';
          this.pistaTexto = "CORRE! AGAFA UNS CALCETINS I LLANÇA´LS TAN LLUNY COM PUGUES, VÉS A PER ELLS I REPETEIX L'ACCIÓ TOTES LES VEGADES POSSIBLES.";
          break;
        case 5:
          this.textoJuego = 'QUÈ OBJECTE ÉS?';
          this.pistaTexto = '';
          this.numeroPista = '';
          break;
        case 6:
          this.textoJuego = '';
          this.solucion = 'LA CADIRA';
          this.numeroPista = '';
          this.imagen = 'cadira.jpg';
          this.mensaje = '¡RESPIREM!';
          this.iniciarTiempoSolucion();
          break;
        default:
          break;
      }
    },
    juegoMaterial2(faseJuego) {
      switch (faseJuego) {
        case 1:
          this.textoJuego = "BALLA AL RITME DE LA MÚSICA, LA PERSONA QUE ESTIGA A PROP TEU HAURÀ DE CANTAR UNA CANÇÓ I TU HAURÀS DE BALLAR A RITME D'ELLA.";
          this.numeroPista = 'Pista 1';
          this.pistaTexto = 'ÉS UNA FONT D´ENERGIA';
          break;
        case 2:
          this.textoJuego = "TOMBA'T CAP AMUNT, POSA LES MANS AL TEU CULET I ALÇA LES CAMES ESTIRADES TANTES VEGADES COM PUGUES.";
          this.numeroPista = 'Pista 2';
          this.pistaTexto = 'ÉS UNA FONT D´ENERGIA RENOVABLE';
          break;
        case 3:
          this.textoJuego = "FES COM SI ESTIGUESSIS ASSEGUT EN L'AIRE I TOCA LA PARET AMB LA ESQUENA.";
          this.numeroPista = 'Pista 3';
          this.pistaTexto = 'DÓNA LLUM I CALOR';
          break;
        case 4:
          this.textoJuego = "CREA UN PONT AMB EL TEU COS. TOMBA'T CAP AVALL RECOLZANT NOMÉS ELS PEUS I LES MANS";
          this.numeroPista = 'Pista 4';
          this.pistaTexto = 'ÉS RODÓ I TÉ RAIGS';
          break;
        case 5:
          this.textoJuego = 'QUÈ FONT D´ENERGIA ÉS?';
          this.pistaTexto = '';
          this.numeroPista = '';
          break;
        case 6:
          this.textoJuego = '';
          this.solucion = 'EL SOL';
          this.numeroPista = '';
          this.imagen = 'sol.jpg';
          this.mensaje = '¡RESPIREM!';
          this.iniciarTiempoSolucion();
          break;
        default:
          break;
      }
    },
    juegoMaterial3(faseJuego) {
      switch (faseJuego) {
        case 1:
          this.textoJuego = 'CORRE AL LLOC INTENTANT PORTAR ELS TEUS GENOLLS A EL PIT';
          this.numeroPista = 'Pista 1';
          this.pistaTexto = 'ESTÀ FORMADA PER AIGUA';
          break;
        case 2:
          this.textoJuego = 'SEU A TERRA I TOCA LA PUNTA DELS TEUS PEUS AMB LA PUNTA DELS DITS DE LA MÀ.';
          this.numeroPista = 'Pista 2';
          this.pistaTexto = 'ES CREA PER UN CANVI D´ESTAT';
          break;
        case 3:
          this.textoJuego = "RÀPID! CREA AL TEU CAP UN RECORREGUT PER LA TEUA CASA I FES-HO ABANS QUE S'ACABE EL TEMPS.";
          this.numeroPista = 'Pista 3';
          this.pistaTexto = 'ÉS SÒLIDA';
          break;
        case 4:
          this.textoJuego = 'CORRE EN EL LLOC MOVENT CAMES I BRAÇOS.';
          this.numeroPista = 'Pista 4';
          this.pistaTexto = 'CAU QUAN FA FRED I ES POT FER NINOTS AMB ELLA';
          break;
        case 5:
          this.textoJuego = 'QUÈ ÉS?';
          this.pistaTexto = '';
          this.numeroPista = '';
          break;
        case 6:
          this.textoJuego = '';
          this.solucion = 'LA NEU';
          this.numeroPista = '';
          this.imagen = 'neu.jpg';
          this.mensaje = '¡RESPIREM!';
          this.iniciarTiempoSolucion();
          break;
        default:
          break;
      }
    },
    juegoMaquina1(faseJuego) {
      switch (faseJuego) {
        case 1:
          this.textoJuego = "HAS D´ANAR A LA CADIRA MÉS PROPERA. SEU I ALÇA'T TOTES LES VEGADES QUE PUGUES.";
          this.numeroPista = 'Pista 1';
          this.pistaTexto = 'ÉS UNA MÀQUINA COMPOSTA';
          break;
        case 2:
          this.textoJuego = "AGAFA 2 LLIBRES (CADA UN EN UNA MÀ), POSA'T A PEU COIX I MANTINGUES L'EQUILIBRI";
          this.numeroPista = 'Pista 2';
          this.pistaTexto = 'FUNCIONA AMB FORÇA HUMANA';
          break;
        case 3:
          this.textoJuego = "CERCA UNA BOTELLA D'AIGUA I AGAFA-LA I DEIXA-LA EN EL SÒL TANTES VEGADES COM PUGUES.";
          this.numeroPista = 'Pista 3';
          this.pistaTexto = 'TÉ DUES RODES';
          break;
        case 4:
          this.textoJuego = 'IMPROVISA! PENSA EN UNA CANÇÓ I BALLA-LA COM TU VULGUES';
          this.numeroPista = 'Pista 4';
          this.pistaTexto = 'S´UTILITZA PER FER SPORT';
          break;
        case 5:
          this.textoJuego = 'QUÈ MÀQUINA ÉS?';
          this.pistaTexto = '';
          this.numeroPista = '';
          break;
        case 6:
          this.textoJuego = '';
          this.solucion = 'LA BICICLETA';
          this.numeroPista = '';
          this.imagen = 'bicicleta.jpg';
          this.mensaje = '¡RESPIREM!';
          this.iniciarTiempoSolucion();
          break;
        default:
          break;
      }
    },
    juegoMaquina2(faseJuego) {
      switch (faseJuego) {
        case 1:
          this.textoJuego = "AGAFA EL PRIMER QUE TINGUES A MÀ (QUE NO ES PUGA TRENCAR I QUE NO PESE MOLT) PER EXEMPLE: UNS CALCETINS I PORTA-LOS D'UN EXTREM A UN ALTRE DEL TEU SALÓ.";
          this.numeroPista = 'Pista 1';
          this.pistaTexto = 'ÉS UNA MÀQUINA COMPOSTA';
          break;
        case 2:
          this.textoJuego = 'PUJA I BAIXA ELS TEUS BRAÇOS TANTES VEGADES COM PUGUES.';
          this.numeroPista = 'Pista 2';
          this.pistaTexto = 'FUNCIONA AMB ENERGIA ELÈCTRICA';
          break;
        case 3:
          this.textoJuego = "IMAGINA QUE SALTES AMB LA CORDA, DÓNA TOTS ELS SALTS POSSIBLES SENSE MOURE'T DEL LLOC.";
          this.numeroPista = 'Pista 3';
          this.pistaTexto = 'ES GRAN';
          break;
        case 4:
          this.textoJuego = "TOMBA'T A TERRA CAP AMUNT I OBRI I TANCA LES CAMES TANTES VEGADES COM PUGUES.";
          this.numeroPista = 'Pista 4';
          this.pistaTexto = 'SERVEIX PER NETEJAR LA ROBA';
          break;
        case 5:
          this.textoJuego = 'QUÈ MÀQUINA ÉS?';
          this.pistaTexto = '';
          this.numeroPista = '';
          break;
        case 6:
          this.textoJuego = '';
          this.solucion = 'LA RENTADORA';
          this.numeroPista = '';
          this.imagen = 'rentadora.jpg';
          this.mensaje = '¡RESPIREM!';
          this.iniciarTiempoSolucion();
          break;
        default:
          break;
      }
    },
    juegoMaquina3(faseJuego) {
      switch (faseJuego) {
        case 1:
          this.textoJuego = "TOMBA'T A TERRA CAP AVALL I MOU ELS BRAÇOS COM SI ESTIGUERES NADANT.";
          this.numeroPista = 'Pista 1';
          this.pistaTexto = 'ÉS UNA MÀQUINA COMPOSTA';
          break;
        case 2:
          this.textoJuego = "EN EL TEU CORREDOR, CORRE DONANT PASSOS MOLT GRANS FINS QUE S'ACABE EL TEMPS (ANADA I TORNADA).";
          this.numeroPista = 'Pista 2';
          this.pistaTexto = 'FUNCIONA AMB ENERGIA ELÈCTRICA';
          break;
        case 3:
          this.textoJuego = "CORRE! AGAFA UNS CALCETINS I LLANÇA´LS TAN LLUNY COM PUGUES, VÉS A PER ELLS I REPETEIX L'ACCIÓ TOTES LES VEGADES POSSIBLES.";
          this.numeroPista = 'Pista 3';
          this.pistaTexto = 'DÓNA CALOR';
          break;
        case 4:
          this.textoJuego = "BALLA A EL RITME DE LA MÚSICA, LA PERSONA QUE ESTIGA A PROP TEU HAURÀ DE CANTAR UNA CANÇÓ I TU HAURÀS DE BALLAR A RITME D'ELLA";
          this.numeroPista = 'Pista 4';
          this.pistaTexto = 'HO UTILITZEN ELS PERRUQUERS PER A SECAR CABELLS';
          break;
        case 5:
          this.textoJuego = 'QUÈ MÀQUINA ÉS?';
          this.pistaTexto = '';
          this.numeroPista = '';
          break;
        case 6:
          this.textoJuego = '';
          this.solucion = 'EL ASSECADOR';
          this.numeroPista = '';
          this.imagen = 'assecador.jpg';
          this.mensaje = '¡RESPIREM!';
          this.iniciarTiempoSolucion();
          break;
        default:
          break;
      }
    },
  },
};
</script>

<style lang="scss">
html {
  background: url('~@/assets/pattern.png') repeat;
}
#app {
  font-family: 'Chilanka', cursive;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  background: url('~@/assets/pattern.png') repeat;
  text-align: center;
  color: #2c3e50;
  margin: 0 auto;
  font-size: 25px;

  img,
  .img-solucion {
    max-width: 300px;
    -webkit-box-shadow: 10px 10px 36px 0px rgba(0,0,0,0.45);
    -moz-box-shadow: 10px 10px 36px 0px rgba(0,0,0,0.45);
    box-shadow: 10px 10px 36px 0px rgba(0,0,0,0.45);
  }

  .img-solucion {
    border: 5px solid black;
    border-radius: 15px;
    margin-bottom: 50px;
  }

  .home,
  .lista-asignaturas {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin: 20px 0;
    padding: 0 10px;
  }

  .tema-naturales,
  .juego {
    padding: 20px 10px;
  }

  p {
    margin-bottom: 35px;
    font-weight: 700;
    cursor: pointer;
    }

  .texto-juego {
    color: #538135;
    margin-bottom: 35px;
  }

  .progress {
    width: 300px;
    margin: 0 auto;
  }

  .pista-bloque,
  .counter {
    display: flex;
    flex-direction: column;
    align-items: center;

    .pista-texto {
      position: relative;
      border: 2px solid #538135;
      background: white;
      padding: 20px 35px;
      margin-bottom: 50px;
      color: #538135;

      .titulo {
        position: absolute;
        background: #D9ECA2;
        width: 150px;
        top: -25px;
        left: calc(50% - 75px);
        padding: 5px 15px;
        border: 1px solid #538135;
      }

      .texto {
        font-weight: bold;
        margin-top: 15px;
        font-size: 20px;
      }
    }
  }

  button {
    font-family: 'Chilanka', cursive;
    position: relative;
    border-radius: 75px;
    background: #F0C7F5;
    border: 2px solid #D54798;
    padding: 25px;
    color: #D54798;
    font-weight: 800;
    font-size: 30px;
    margin-bottom: 50px;
    width: 300px;
    min-height: 145px;
    cursor: pointer;
    -webkit-box-shadow: 10px 10px 36px 0px rgba(0,0,0,0.45);
    -moz-box-shadow: 10px 10px 36px 0px rgba(0,0,0,0.45);
    box-shadow: 10px 10px 36px 0px rgba(0,0,0,0.45);

    &.asignaturas {
      border-radius: 75px;

      img {
        position: absolute;
        width: 50px;
        top: 33px;
        left: 125px;
        opacity: .5;
      }

      &.naturales {
        background: #D9ECA2;
        border: 2px solid #538135;
        color: #538135;
      }
      &.sociales {
        background: #B9F0F3;
        border: 2px solid #2F5496;
        color: #2F5496;
        opacity: .5;
      }
      &.mates {
        background: #E5D4F6;
        border: 2px solid #7030A0;
        color: #7030A0;
        opacity: .5;
      }
      &.caste {
        background: #FD919B;
        border: 2px solid #C00000;
        color: #C00000;
        opacity: .5;
      }
      &.ingles {
        background: #F9D1E5;
        border: 2px solid #EA54B8;
        color: #EA54B8;
        opacity: .5;
      }
    }
  }
}

</style>
