<template>
    <div
        class="drag-area"
        :class="{draging:dragedID}"
        @mousemove="drag($event)"
        @mouseup="dragend($event)"
    >
        <div class="drag-col" data-list="listOne">
            <transition-group name="list" tag="div">
                <div v-for="item in listOne" :key="item">
                    <Card
                        :ref="item"
                        v-if="item !== dragedID"
                        @dragstart="'return false;'"
                        @mousedown.native="dragstart($event, item)"
                    >{{ item }}</Card>
                    <PlaceHolder
                        :ref="item+'-placeholder'"
                        v-if="item === dragedID"
                        :w="dragedNodeWidth"
                        :h="dragedNodeHeight"
                    ></PlaceHolder>
                </div>
            </transition-group>
        </div>

        <div class="drag-col" data-list="listTwo">
            <transition-group name="list" tag="div">
                <div v-for="item in listTwo" :key="item">
                    <Card
                        :ref="item"
                        v-if="item !== dragedID"
                        @dragstart="'return false;'"
                        @mousedown.native="dragstart($event, item)"
                    >{{ item }}</Card>
                    <PlaceHolder
                        :ref="item+'-placeholder'"
                        v-if="item === dragedID"
                        :w="dragedNodeWidth"
                        :h="dragedNodeHeight"
                    ></PlaceHolder>
                </div>
            </transition-group>
        </div>
        <card
            v-show="dragedID"
            ref="freeCard"
            class="freeCard"
            isTransparent="true"
        >{{dragedNode?dragedNode.$slots.default[0].text:''}}</card>
    </div>
</template>

<script>
import Card from './components/Card';
import PlaceHolder from './components/PlaceHolder';
export default {
    name: 'App',
    components: {
        Card,
        PlaceHolder
    },
    data() {
        return {
            listOne: [
                '\n \n \n \none\n \n \n \n',
                ' \n \n \n \n \n \ntwo \n \n \n \n \n \n',
                'three',
                '\n \nfour \n \n'
            ],
            listTwo: ['five', '\n six \n', '\n \nseven \n \n'],

            dragedID: null,
            move: false,
            currList: { list: null, index: null },
            hoveredCard: null,
            dragedNode: null,
            dragedNodeWidth: null,
            dragedNodeHeight: null,
            dragedNodeOffset: [0, 0],

            freeCard: null,
            moving: false
        };
    },

    methods: {
        dragstart(ev, ID) {
            // register the node to drag
            this.dragedID = ID;
            this.move = true;
            this.dragedNode = this.$refs[ID][0];
            const node = this.dragedNode.$el;
            this.freeCard = this.$refs['freeCard'].$el;
            const freeCard = this.freeCard;
            // registring the width, height & initial offset from mouse to use later
            this.dragedNodeWidth = this.dragedNode.$el.getBoundingClientRect().width;
            this.dragedNodeHeight = this.dragedNode.$el.getBoundingClientRect().height;
            this.dragedNodeOffset = [ev.offsetX, ev.offsetY];
            // registring the origin list of this node & the index of this element in it
            this.dragFromList = ev.target.closest('.drag-col').dataset.list;
            const index = this[this.dragFromList].findIndex(
                el => el === this.dragedID
            );
            this.currList = { list: this.dragFromList, index };
            // setting the initial styling
            this.freeCard.style = '';
            this.freeCard.style.top = `${ev.pageY -
                this.dragedNodeOffset[1]}px`;
            this.freeCard.style.left = `${ev.pageX -
                this.dragedNodeOffset[0]}px`;
            this.freeCard.style.width = `${this.dragedNodeWidth}px`;
        },

        drag(ev) {
            const vm = this;
            const dragedID = this.dragedID;
            if (!this.move) return;
            const node = this.dragedNode.$el;
            const freeCard = this.freeCard;
            // moving the node to the mousee pos & keeping the same distance/offset as the start
            freeCard.style.top = `${ev.pageY - this.dragedNodeOffset[1]}px`;
            freeCard.style.left = `${ev.pageX - this.dragedNodeOffset[0]}px`;
            // get the current the drag col witch the mouse is over now
            const dragCol = document
                .elementsFromPoint(ev.pageX, ev.pageY)
                .find(el => el.classList.contains('drag-col'));
            if (!dragCol) return;
            const dragTo = dragCol.dataset.list;
            const isACardHoverdByMouse = document
                .elementsFromPoint(ev.pageX, ev.pageY)
                .find(function(el) {
                    const a = el.classList.contains('card');
                    return a && el.textContent !== vm.dragedID;
                });
            const currList = this.currList;
            if (isACardHoverdByMouse) {
                if (this.moving) return;
                // find the index of hovered card in dragTo
                const hoveredInd = this[dragTo].findIndex(
                    el => el === isACardHoverdByMouse.textContent
                );
                // register the draged element
                const element = this[currList.list].find(el => el === dragedID);
                // remove the draged element from the currList
                this[currList.list].splice(currList.index, 1);
                // add the draged element to to dragTo list
                this[dragTo].splice(hoveredInd, 0, element);
                // update currList to dragTo List
                currList.list = dragTo;
                currList.index = hoveredInd;
                this.moving = true;
                setTimeout(() => {
                    this.moving = false;
                }, 250);
            } else if (currList.list !== dragTo) {
                if (this.moving) return;

                // register the draged element
                const element = this[currList.list].find(el => el === dragedID);
                // remove the draged element from the currList
                this[currList.list].splice(currList.index, 1);
                // add the draged element to to dragTo list
                this[dragTo].push(element);
                // update currList to dragTo List
                currList.list = dragTo;
                currList.index = this[dragTo].length - 1;

                this.moving = true;
                setTimeout(() => {
                    this.moving = false;
                }, 250);
            }
        },

        dragend(ev) {
            if (!this.dragedID) return;
            this.move = false;

            setTimeout(() => {
                this.resetAll();
            }, 110);

            const originalNode = this.$refs[this.dragedID + '-placeholder'][0];

            const style = this.freeCard.style;

            let xOffset =
                style.left.replace('px', '') * 1 -
                originalNode.$el.getBoundingClientRect().x * 1;
            let yOffset =
                style.top.replace('px', '') * 1 -
                originalNode.$el.getBoundingClientRect().y * 1;

            this.freeCard.style.transform = `scale(1) translate(${-xOffset}px, ${-yOffset}px)`;
        },

        resetAll() {
            this.dragedNode.$el.style = null;

            this.dragedID = null;
            this.justStarted = false;
            this.currList = { list: null, index: null };
            this.hoveredCard = null;
            this.dragedID = null;
        }
    }
};
</script>

<style>
*,
*::before,
*::after {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}
body {
    font-family: 'Lato', sans-serif;
    font-size: 16px;
    text-transform: capitalize;
    -webkit-user-drag: none;
    user-select: none;
    -moz-user-select: none;
    -webkit-user-drag: none;
    -webkit-user-select: none;
    -ms-user-select: none;
}
.drag {
    cursor: grabbing !important;
    background-color: #222 !important;
}
.draging {
    cursor: grabbing !important;
}
.drag-area {
    margin: 2rem;
    height: 100vh;
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    align-items: start;
}
.drag-col {
    width: 100%;
    height: 100%;
    margin: 1rem;
}
.freeCard {
    position: absolute;
    transition: transform 0.1s ease-in-out;
    z-index: 10000;
}
.list-move {
    transition: all 0.2s ease;
}

.list-leave-active {
    position: absolute;
}
</style>