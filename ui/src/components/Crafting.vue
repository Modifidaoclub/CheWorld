<template>
  <div class="alertModel">
    <div class="content">
      <div class="logo"><img src="@/assets/images/logo2.png" alt=""></div>
      <div class="close" @click="closeCrafting"></div>
      <div class="tab" id="tab1">
        <div class="left">
          <div class="hd">
            <a href="javascript:;" :class="[craftingIndex===1 ? 'current' : '',]"
               @click="setCraftingIndex(1)">Equipment</a>
            <a href="javascript:;" :class="[craftingIndex===2 ? 'current' : '',]" @click="setCraftingIndex(2)">Food</a>
            <a href="javascript:;" :class="[craftingIndex===3 ? 'current' : '',]" @click="setCraftingIndex(3)">TOOLS</a>
            <a href="javascript:;" :class="[craftingIndex===4 ? 'current' : '',]"
               @click="setCraftingIndex(4)">Materials</a>
          </div>
          <div class="bd">
            <div class="model">
              <ul>
                <li v-for="(equipment,index) in configs.equipments" :key="index" v-show="craftingIndex===1"
                    :class="[ equipment.showDetails ? 'current' : '' ]">
                  <h2 class="h2tit" @click="showDetail(index)"><img :src="equipment.icon" alt=""><span>{{
                      equipment.name
                    }}</span></h2>
                  <dl :style="{ display: equipment.showDetails ? 'block' : 'none' }">
                    <dd v-for="(item,index) in equipment.list" :key="index">
                      <a href="javascript:;" class="dis"
                         @click="doSelect(item)">{{ item.name }}</a>
                    </dd>
                  </dl>
                </li>
                <li v-for="(food,index) in configs.foods" :key="index" v-show="craftingIndex===2"
                    :class="[ food.showDetails ? 'current' : '' ]">
                  <h2 class="h2tit" @click="showFoodDetail(index)"><img :src="food.icon" alt=""><span>{{
                      food.name
                    }}</span></h2>
                  <dl :style="{ display: food.showDetails ? 'block' : 'none' }">
                    <dd v-for="(item,index) in food.list" :key="index">
                      <a href="javascript:;" class="dis"
                         @click="doSelect(item)">{{ item.name }}</a>
                    </dd>
                  </dl>
                </li>
              </ul>
            </div>
          </div>
          <div class="check">
            <span>Show craftable gea</span>
          </div>
        </div>
        <div class="right">

          <div class="none" v-if="!selected">Select equipment on the left to view crafting recipes</div>

          <div class="type1" v-if="!complate && selected">
            <div class="title">Ingredient Recipe</div>
            <div class="list pair">
              <ul>
                <li v-for="(pair,index) in selected.pairs" :key="index">
                  <img src="@/assets/images/set1.png" alt="">
                  <div class="num">{{ pair.value }}</div>
                  <div class="slide" style="display: block">
                    <div class="if1">
                      <div class="icn"><img src="@/assets/images/set1.png" alt=""></div>
                      <div class="ri">
                        <p>
                          <b>Item: </b>{{ pair.name }}
                        </p>
                        <p>
                          <b>Type: </b>{{ pair.type }}
                        </p>
                        <p>
                          <b>Quantity: </b>{{ pair.value }}
                        </p>
                      </div>
                    </div>
                    <div class="if2">
                      {{ pair.desc }}
                    </div>
                  </div>
                </li>
              </ul>
            </div>
            <div class="title2"><img :src="selected.icon" alt=""><span>{{ selected.name }}</span></div>
            <div class="num">
              <i class="ic1" @click="decrCraftingNumber"></i>
              <input type="text" :value="craftingNumber" class="words">
              <i class="ic2" @click="incrCraftingNumber"></i>
            </div>
            <a href="javascript:;" v-loading="loading" class="Craft" @click="doCrafting">Craft</a>
          </div>

          <div class="type2" v-if="complate">
            <div class="title">Crafting Complete</div>
            <div class="list2">
              <ul>
                <li v-for="(item,index) in addedItems" :key="index">
                  <div class="infor1">
                    <div class="icon"><img :src="item.icon" alt=""></div>
                    <div class="tit">{{ item.name }}</div>
                  </div>
                  <div class="dec">
                    <p>TIERS:{{ item.tiers }}</p>
                    <p>XP: 0</p>
                  </div>
                </li>
              </ul>
            </div>
            <a href="javascript:;" class="Craft" @click="onClickCompleteConform">confirm</a>
          </div>

        </div>
      </div>
    </div>
  </div>
</template>

<script>
import {mapActions, mapMutations, mapState} from "vuex";
import {ElMessage} from "element-plus";
import {composite_config, item_subtypes} from "@/config/item.js";
import {ITEM_ICONS, ITEM_SLOTS, ITEM_TIERS, ITEMS} from "@/system/GameData.js";
import {getResConfigById, getResConfigByKey, ResType} from "@/config/res_conf.js";

export default {
  name: 'CraftingComponent',
  components: {},
  mounted() {
    this.init();
  },
  computed: mapState(['wallet_address', "adventurer", "craftingIndex", "craftingNumber"]),
  data() {
    return {
      selected: null,
      complate: null,
      configs: {
        equipments: {
          'Neck': {
            icon: "images/fd7.png",
            name: "Neck",
            showDetails: false,
            list: [],
            subtype: item_subtypes.head
          },
          'Ring': {
            icon: "images/fd8.png",
            name: "Ring",
            showDetails: false,
            list: [],
            subtype: item_subtypes.head
          },
          'Chest': {
            icon: "images/fd2.png",
            name: "Chest",
            showDetails: false,
            list: [],
            subtype: item_subtypes.head
          },
          'Hand': {
            icon: "images/fd6.png",
            name: "Hand",
            showDetails: false,
            list: [],
            subtype: item_subtypes.head
          },
          'Waist': {
            icon: "images/fd3.png",
            name: "Waist",
            showDetails: false,
            list: [],
            subtype: item_subtypes.head
          },
          'Foot': {
            icon: "images/fd4.png",
            name: "Foot",
            showDetails: false,
            list: [],
            subtype: item_subtypes.head
          },
          'Head': {
            icon: "images/fd1.png",
            name: "Head",
            showDetails: false,
            list: [],
            subtype: item_subtypes.head
          },
          'Weapon': {
            icon: "images/fd5.png",
            name: "Weapon",
            showDetails: false,
            list: [],
            subtype: item_subtypes.head
          },
        },
        foods: [
          {
            icon: "images/ic6.png",
            name: "ROAST MEAT",
            showDetails: false,
            list: []
          },
          {
            icon: "images/ic6.png",
            name: "FISH SOUP",
            showDetails: false,
            list: []
          },
        ]
      },
      loading: false,
      addedItems: [],
    }
  },
  methods: {
    ...mapActions(['connect_wallet', "composite"
    ]),
    ...mapMutations([
      'setShowCrafting', "setCraftingIndex", "setAdventurerBag",
      "setCraftingNumber",
    ]),
    init() {
      for (let i = 0; i < composite_config.length; i++) {
        const comp = composite_config[i];
        const pairs = comp.composite.split(",").map(pair => {
          let [key, value] = pair.split("|");
          key = parseInt(key);
          let res_config = getResConfigById(key);
          return {
            key: key,
            value: parseInt(value),
            name: res_config.name,
            desc: res_config.inform,
            type: ResType[res_config.type]
          };
        });
        console.log(pairs);

        if (comp.type == 2) {
          this.configs.foods[0].list.push({
            id: comp.id,
            name: "ROAST MEAT",
            pairs: pairs
          })
        }

        if (comp.type == 1) {
          let item_name = ITEMS[comp.name];
          let key = item_name.replace(' ', '');
          let item_slot = ITEM_SLOTS[key];
          switch (item_slot) {
            case 'Neck':
              this.configs.equipments.Neck.list.push({
                id: comp.id,
                target: comp.name,
                name: item_name,
                pairs: pairs,
                icon: '/images/' + ITEM_ICONS[item_slot]
              });
              break;
            case 'Ring':
              this.configs.equipments.Ring.list.push({
                id: comp.id,
                name: item_name,
                pairs: pairs,
                icon: '/images/' + ITEM_ICONS[item_slot]
              });
              break;
            case 'Chest':
              this.configs.equipments.Chest.list.push({
                id: comp.id,
                name: item_name,
                pairs: pairs,
                icon: '/images/' + ITEM_ICONS[item_slot]
              });
              break;
            case 'Hand':
              this.configs.equipments.Hand.list.push({
                id: comp.id,
                name: item_name,
                pairs: pairs,
                icon: '/images/' + ITEM_ICONS[item_slot]
              });
              break;
            case 'Waist':
              this.configs.equipments.Waist.list.push({
                id: comp.id,
                name: item_name,
                pairs: pairs,
                icon: '/images/' + ITEM_ICONS[item_slot]
              });
              break;
            case 'Foot':
              this.configs.equipments.Foot.list.push({
                id: comp.id,
                name: item_name,
                pairs: pairs,
                icon: '/images/' + ITEM_ICONS[item_slot]
              });
              break;
            case 'Head':
              this.configs.equipments.Head.list.push({
                id: comp.id,
                name: item_name,
                pairs: pairs,
                icon: '/images/' + ITEM_ICONS[item_slot]
              });
              break;
            case 'Weapon':
              this.configs.equipments.Weapon.list.push({
                id: comp.id,
                name: item_name,
                pairs: pairs,
                icon: '/images/' + ITEM_ICONS[item_slot]
              });
              break;

          }

          console.log(item_name, item_slot);
        }
      }

    },
    closeCrafting() {
      this.setShowCrafting(false);
    },
    incrCraftingNumber() {
      this.setCraftingNumber(this.craftingNumber + 1);
    },
    decrCraftingNumber() {
      this.setCraftingNumber(this.craftingNumber - 1);
    },
    diffBags(bag1, bag2) {
      const removedItems = [];
      const addedItems = [];

      for (const item in bag1) {
        if (item === "mutated") {
          continue;
        }
        if (bag1[item].id === 0) {
          removedItems.push(item);
        }
      }

      for (const item in bag2) {
        if (item === "mutated") {
          continue;
        }
        if (bag2[item].id !== 0) {
          addedItems.push(item);
        }
      }

      return {
        removedItems,
        addedItems,
      };
    },
    async doCrafting() {
      // console.log(id, name, pairs)
      if (this.loading === true) {
        return;
      }
      try {
        this.loading = true;
        let events = await this.composite({config_id: this.selected.id, times: this.craftingNumber});
        let event = events[0];
        let bag = event.data.data.adventurerStateWithBag.bag;
        let reward = event.data.data.reward;
        let times = event.data.data.times;
        console.log(bag);
        let diff = this.diffBags(this.adventurer.bag, bag);
        console.log(diff);
        if (diff.length > 0) {
          this.showInformation = true;
        }

        for (let i = 0; i < diff.addedItems.length; i++) {
          let key = diff.addedItems[i];
          let info = bag[key];
          let name = ITEMS[info.id];
          let tier = ITEM_TIERS[name];
          let slot = ITEM_SLOTS[name];
          let icon = 'images/' + ITEM_ICONS[slot];
          console.log({name, info, tier, slot, icon});
          this.addedItems.push({
            'id': info.id,
            'name': name,
            'slot': slot,
            'tiers': tier,
            'icon': icon,
          })
        }

        for (let i = 0; i < reward.roast_meat * times; i++) {
          this.addedItems.push({
            'id': 0,
            'name': 'Roast Meat',
            'tiers': 0,
            'icon': '/images/set1.png',
          })
        }

        this.setAdventurerBag(bag);
        this.complate = true
      } catch (e) {
        console.log(e)
      } finally {
        this.loading = false;
      }


      // ElMessage({
      //   message: 'Congrats, this is a success message',
      //   type: 'success',
      // })
    },
    showDetail(subid) {
      console.log("subid", subid)
      this.configs.equipments[subid].showDetails = !this.configs.equipments[subid].showDetails
      // this.setEquipmentShowDetail(subid)
    },
    showFoodDetail(subid) {
      this.configs.foods[subid].showDetails = !this.configs.foods[subid].showDetails
    },
    async doSelect(item) {
      if (this.loading) {
        return;
      }

      console.log("select", item)
      // await this.addItem();
      // ElMessage({
      //   message: 'Congrats, this is a success message.',
      //   type: 'success',
      // })
      this.selected = item;
      this.complate = null;
    },
    onClickCompleteConform() {
      this.selected = null;
      this.complate = null;
    }
  }
}
</script>

<style scoped>
.right > div > div.list > ul > li {
  width: 78px;
  height: 78px;
  display: inline-block;
  background-size: 100%;
  cursor: pointer;
  vertical-align: middle;
  position: relative;
}

.right > div > div.list > ul > li > img {
  object-fit: contain;
  height: 100%;
}

.right > div > div.list > ul > li > .num {
  height: 10px;
  width: 10px;
  position: absolute;
  color: white;
  text-shadow: -2px -2px 0 black, 2px -2px 0 black, -2px 2px 0 black, 2px 2px 0 black;
  right: 10px;
  bottom: 10px;
  z-index: 44;
  font-size: 14px;
}

.pair ul li:hover .slide {
  visibility: visible;
  opacity: 1;
  z-index: 5;
  bottom: 70px;
}


.pair ul li .slide {
  background: url(../images/common_float_frame_03s.png) no-repeat center center;

  background-size: 100% 100%;

  width: 346px;

  height: 205px;

  position: absolute;

  bottom: 70px;

  left: 100%;

  font-size: 14px;

  text-align: left;

  padding: 14px 21px;
  transition: all 0.4s;

  visibility: hidden;
  opacity: 0;
  z-index: -1;
  bottom: 80px;
}
</style>