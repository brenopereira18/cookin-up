<script lang="ts">
import { itensDeLista1EstaoEmLista2 } from "@/operacoes/listas";
import { obterReceitas } from "@/http/index";
import type IReceitas from "@/interfaces/IReceitas";
import CardReceitas from "./CardReceitas.vue";
import BotaoPrincipal from "./BotaoPrincipal.vue";
import type { PropType } from "vue";

export default {
  props: {
    ingredientes: { type: Array as PropType<string[]>, required: true },
  },
  data() {
    return {
      receitasEncontradas: [] as IReceitas[],
    };
  },
  async created() {
    const receitas = await obterReceitas();
    this.receitasEncontradas = receitas.filter((receita) => {
      const possoFazerReceita = itensDeLista1EstaoEmLista2(
        receita.ingredientes,
        this.ingredientes
      );

      return possoFazerReceita;
    });
  },
  components: { CardReceitas, BotaoPrincipal },
  emits: ["editarReceitas"],
};
</script>

<template>
  <section class="mostrar-receitas">
    <h2 class="cabecalho titulo-receitas">Receitas</h2>
    <p class="paragrafo-lg resultados-encontrados">
      Resultados encontrados: {{ receitasEncontradas.length }}
    </p>

    <div v-if="receitasEncontradas.length > 0" class="receitas-wrapper">
      <p class="paragrafo-lg informacoes">
        Veja as opções de receitas que encontramos com os ingredientes que você
        tem por aí!
      </p>
      <ul class="receitas">
        <li v-for="receita in receitasEncontradas" :key="receita.nome">
          <CardReceitas :receita="receita" />
        </li>
      </ul>
    </div>
    <div v-else class="receitas-nao-encontradas">
      <p class="paragrafo-lg receitas-nao-encontradas">
        Ops, não encontramos resultados para sua combinação. Vamos tentar de
        novo?
      </p>
      <img
        src="../assets/images/sem-receitas.png"
        alt="Desenho de um ovo quebrado"
      />
    </div>
  </section>
  <BotaoPrincipal texto="Editar lista" @click="$emit('editarReceitas')" />
</template>

<style scoped>
.mostrar-receitas {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
}
.titulo-receitas {
  color: #3d6d4a;
  margin-bottom: 1.5rem;
}

.resultados-encontrados {
  color: #3d6d4a;
  margin-bottom: 0.5rem;
}

.receitas-wrapper {
  margin-bottom: 3.5rem;
}

.informacoes {
  margin-bottom: 2rem;
}

.receitas {
  display: flex;
  justify-content: center;
  gap: 1.5rem;
  flex-wrap: wrap;
}

@media only screen and (max-width: 767px) {
  .receitas-wrapper {
    margin-bottom: 2rem;
  }
}
</style>
