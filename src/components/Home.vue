<template>
  <div class="mb-6 bg-red-800 text-left ml-64 flex">
<!--    {{ priceObj }}-->
    <div class="mr-5">
      <svg width="360" height="302" viewBox="0 -20 300 200" style="border: 1px solid red; font-size: 11px;">
        <svg-bar
          :height="200"
          :fill="'#000'"
          :text-color="'#fff'"
          :value="priceObj.totalPrice()"
          label="TP"
          :x="0"
        />
        <svg-bar
          :height="(priceObj.discountedPrice() / priceObj.totalPrice() * 200)"
          :fill="'#000'"
          :text-color="'#fff'"
          :value="priceObj.discountedPrice()"
          label="DP"
          :x="60"
        />
        <svg-bar
          :height="(priceObj.cost / priceObj.totalPrice() * 200)"
          :fill="'#000'"
          :text-color="'#fff'"
          :value="priceObj.cost"
          label="COST"
          :x="120"
        />
        <svg-bar
          :height="(priceObj.markup.absoluteValue() / priceObj.totalPrice() * 200)"
          :fill="'green'"
          :text-color="'#fff'"
          :value="priceObj.discountedMarkup.absoluteValue().toFixed(0)"
          label="MARKUP"
          :x="180"
        />
        <svg-bar
          :height="(priceObj.discount.absoluteValue() / priceObj.totalPrice() * 200)"
          :fill="'blue'"
          :text-color="'#fff'"
          :value="priceObj.discount.absoluteValue().toFixed(0)"
          label="COST"
          sub-label="DISCOUNT"
          :x="240"
        />
      </svg>
    </div>


    <div class="p-1 mr-5" style="font-size: 13px;">
      <div class="py-1">
        <div class="px-1 mb-5">
          <div>Себестоимость (=COST)</div>
          <input type="text" v-model="priceObj.cost" class="w-28">
        </div>
        <div class="px-1 mb-5">
          <div>Наценка на COST (=MARKUP)</div>
          <div class="flex justify-between">
            <input type="text" v-model="priceObj.markup.multiplierOnCost" style="width: 80px;">
            <div>+{{ priceObj.markup.absoluteValue().toFixed(2) }}</div>
          </div>
        </div>
        <div class="px-1 mb-5">
          <div>Total Price (=TP)</div>
          <div>= {{ priceObj.totalPrice().toFixed(0) }}</div>
        </div>
        <div class="px-1 mb-5">
          <div>Скидка на TP (=TP-DISCOUNT)</div>
          <input type="text" v-model="priceObj.discount.multiplierOnTotalPrice" style="width: 80px;">
          <br><br>
          <div>Скидка на COST (=COST-DISCOUNT)</div>
          <div>{{ priceObj.discount.multiplierOnCost.toFixed(3) }} (-{{priceObj.discount.absoluteValue()}})</div>
        </div>
        <div class="px-1 mb-5">
          <div>Цена со скидкой (=DP)</div>
          <div>= {{ priceObj.discountedPrice().toFixed(2) }}</div>
        </div>
      </div>


    </div>
<!--    <table>-->
<!--      <tr v-for="(item, vIdx) in priceObj.displayedItems" :key="vIdx">-->
<!--        <td class="pr-5" v-html="item.label"/>-->
<!--        <td v-html="item.val()"/>-->
<!--      </tr>-->
<!--    </table>-->
    <div class="flex bg-white h-6 px-2">
      <div class="old-price-span mr-3" style="color: black;">{{ priceObj.totalPrice().toFixed(0) }}</div>
      <div style="color: orange; ">{{ priceObj.discountedPrice().toFixed(0) }}</div>
    </div>
  </div>

</template>
<script>
  import SvgBar from './SvgBar.vue'

  function r2d(val) {
    return Math.round(val * 100) / 100
  }
  class priceObject {
    cost = null
    markup = {
      multiplierOnCost: null, // percentage on cost
      percentageOnCost: () => this.markup.multiplierOnCost * 100,
      absoluteValue: () => this.cost * this.markup.multiplierOnCost,
    }
    totalPrice = () => Number(this.cost) + Number(this.markup.absoluteValue()) // crossed out price
    discount = {
      _priceObject: this,
      multiplierOnTotalPrice: null,
      get multiplierOnCost () {
        return this.absoluteValue() / this._priceObject.cost // this._priceObject.totalPrice() * this.multiplierOnTotalPrice
      },
      percentageOnCost: () => this.discount.multiplierOnCost * 100,
      absoluteValue: () => this.totalPrice() * this.discount.multiplierOnTotalPrice,
      percentageOnTotalPrice: () => this.discount.multiplierOnTotalPrice * 100, // percentage on crossed out price
    }
    discountedPrice = () => this.totalPrice() - this.discount.absoluteValue()
    discountedMarkup = { // markup minus discount (both on cost)
      multiplierOnCost: () => this.markup.multiplierOnCost - this.discount.multiplierOnCost,
      percentageOnCost: () => this.markup.percentageOnCost() - this.discount.percentageOnCost(),
      absoluteValue: () => this.cost * this.discountedMarkup.multiplierOnCost(),
    }


    displayedItems = [
      { label: 'Cost', val: () => `${this.cost} rub` },
      { label: 'Markup on cost', val: () => `${r2d(this.markup.percentageOnCost())} % (${r2d(this.markup.absoluteValue())} rub)` },
      { label: 'Total Price', val: () => `${this.totalPrice()} rub` },
      { label: 'Discount on Total Price', val: () => `${this.discount.percentageOnTotalPrice()} %` },
      { label: 'Discount on cost', val: () => `${r2d(this.discount.percentageOnCost())} % (${r2d(this.discount.absoluteValue())} rub)` },
      { label: 'Discounted Price', val: () => `${r2d(this.discountedPrice())} rub` },
      { label: 'Discounted Markup', val: () => `${r2d(this.discountedMarkup.percentageOnCost())} % (${r2d(this.discountedMarkup.absoluteValue())} rub)` },
    ]

    constructor (cost, markup, discount) {
      this.cost = cost
      this.markup.multiplierOnCost = markup
      this.discount.multiplierOnTotalPrice = discount
    }
  }

  export default {
    components: { SvgBar },
    data () {
      return {
        priceObj: new priceObject(4000, .15, .03),
      }
    }
  }
</script>

<style>
  .old-price-span {
    position: relative;
  }
  .old-price-span:after {
    content: '';
    position: absolute;
    left: -5px;
    right: -5px;
    top: 12px;
    border-top: 2px solid red;
    transform: rotateZ(-8deg);
  }
</style>