<template>
  <div id="app">
    <Header />
    <div class="board">
      <div class="lane">
        <h2 id="lane-title">Backlog</h2>
        <Container
          group-name="trello"
          @drag-start="handleDragStart('backlog', $event)"
          @drag="handleDrop"
          :get-child-payload="getChildPayload"
        >
          <Draggable v-for="card in cards.backlog" :key="card.id">
            <Card>{{ card.text }}</Card>
          </Draggable>
        </Container>
      </div>
      <div class="lane">
        <h2 id="lane-title">Desenvolvimento</h2>
        <Container
          group-name="trello"
          @drag-start="handleDragStart('dev', $event)"
          @drag="handleDrop"
          :get-child-payload="getChildPayload"
        >
          <Draggable v-for="card in cards.dev" :key="card.id">
            <Card>{{ card.text }}</Card>
          </Draggable>
        </Container>
      </div>
      <div class="lane">
        <h2 id="lane-title">Teste</h2>
        <Container
          group-name="trello"
          @drag-start="handleDragStart('test', $event)"
          @drag="handleDrop"
          :get-child-payload="getChildPayload"
        >
          <Draggable v-for="card in cards.test" :key="card.id">
            <Card>{{ card.text }}</Card>
          </Draggable>
        </Container>
      </div>
      <div class="lane">
        <h2 id="lane-title">Finalizados</h2>
        <Container
          group-name="trello"
          @drag-start="handleDragStart('finalizado', $event)"
          @drag="handleDrop"
          :get-child-payload="getChildPayload"
        >
          <Draggable v-for="card in cards.done" :key="card.id">
            <Card>{{ card.text }}</Card>
          </Draggable>
        </Container>
      </div>
    </div>
  </div>
</template>

<script>
// @ts-nocheck

import Card from "./components/Card.vue";
import Header from "./components/Header.vue";
import initialCards from "./initialCards.js";
// @ts-ignore
import { Container, Draggable } from "vue3-smooth-dnd";

export default {
  name: "App",
  components: { Header, Card, Container, Draggable },

  data: () => ({
    cards: {
      backlog: initialCards.backlog,
      dev: initialCards.dev,
      test: initialCards.test,
    },

    //CONTROLE DO CARD
    draggingCard: {
      lane: "",
      index: -"1",
      cardData: [],
    },
  }),

  methods: {
    handleDragStart: (lane, dragResult) => {
      const { payload, isSource } = dragResult;
      if (isSource) {
        // @ts-ignore
        // @ts-ignore
        this.draggingCard = {
          lane,
          index: payload.index,

          cardData: {
            // @ts-ignore
            ...this.card[lane][payload.index],
          },
        };
      }
    },

    handleDrop(lane, dropResult) {
      const { removedIndex, addedIndex } = dropResult;
      if (lane === this.draggingCard.lane && removedIndex === addedIndex) {
        return;
      }

      if (removedIndex !== null) {
        this.cards[lane].splice(removedIndex, 1);
      }

      if (addedIndex !== null) {
        // @ts-ignore
        this.card[lane].splice(addedIndex, 0, this.draggingCard.cardData);
      }

      // @ts-ignore
      // @ts-ignore
      //solve this problem
      getChildPayload(index){
        return{
          // @ts-ignore
          index,
        }
      }
    }
  },
};
</script>

<style>
.board {
  display: flex;
  justify-content: flex-start;
  margin: 1.2rem 0.8rem;
}
.lane {
  background: var(--color-grey);
  width: 23rem;
  border-radius: 0.8rem;
  box-shadow: 0 0.1rem 0.2rem 0 rgba(33, 33, 33, 0.1);
  margin: 0 0.8rem;
  padding: 0 0.7rem;
}

.lane-title {
  margin-bottom: 0.6rem;
  padding: 0.8rem 0.5rem;
}

.placeholder {
  background: rgba(33, 33, 33, 0.8);
  border-radius: 0.4rem;
  transform: scaleY(0.85);
}
</style>
