<template>
  <div id="app">

  <b-navbar toggleable="sm" type="light" variant="light">

    <div class="container">

        <b-navbar-brand href="#">Магазин</b-navbar-brand>

        <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>

        <b-collapse id="nav-collapse" is-nav>
          <b-navbar-nav>
            <b-nav-item href="#">Ссылка</b-nav-item>
            <b-nav-item href="#" disabled>Отключено</b-nav-item>
          </b-navbar-nav>

        </b-collapse>

    </div>

  </b-navbar>

    <div class="container">
      <b-row>
        <b-col cols="2">

        <div class="mt-2">Поиск</div>
        <b-form-input v-model="text"></b-form-input>
        <br>
        <div class="mt-2">Категории</div>
        <b-list-group>
          <b-list-group-item
            @click="selectCategory(category)"
            v-for="(category,id) in categories"
            :key="id"
            :active="category==currentCat"
          >{{category}}</b-list-group-item>
          <b-list-group-item
            @click="selectCategory('')"
            :active="currentCat==''"
          >Все категории</b-list-group-item>
        </b-list-group>
        
        </b-col>
        <b-col cols="10">
          <b-jumbotron header="Новогодние заварушки" lead="Всё для тех, кто не успел подготовиться заранее">
            <p>Для получения дополнительной информации посетите веб-сайт</p>
            <b-button variant="primary" href="#">Больше информации</b-button>
          </b-jumbotron>
          <hr>
          <b-row>
            <b-col cols="3" v-for="(good,id) in searchInGoods()" :key="id">


              <b-card
                
                :title="good.title"
                :img-src="good.image"
                
                img-alt="Изображение"
                img-top
                tag="article"
                style="max-width: 20rem;"
                class="mb-2"
              >
                <b-card-text>
                  {{ good.description }}
                </b-card-text>
                <h3>{{good.price}}</h3>
                <b-button href="#" variant="primary">Купить</b-button> 
              </b-card>
            </b-col>
          </b-row>


        </b-col>
      </b-row>
    
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data: () => ({
    goods: [],
    categories: [],
    currentCat: '',
    text: ''
  }),
  methods: {
    async getAllGoods() {
      await fetch('/api/goods.php')
            .then(res=>res.json())
            .then(json=>{
              this.goods = json
            })
    },
    async bay(id) {
      let order = {
        id: null,
        good_id: id,
        phone: prompt("Введите номер телефона", "Не помню...")
      };

      let response = await fetch('/api/orders.php', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json;charset=utf-8'
        },
        body: JSON.stringify(order)
      });

      let result = await response.json();
      console.log(result);
    },
    getAllCategories() {
      fetch('/api/categories.php')
            .then(res=>res.json())
            .then(json=>{
              this.categories = json
            })
    },
    searchInGoods() {
      var goods = []
      if(this.currentCat == '') goods = this.goods
      else goods = this.goods.filter(item => (item.category == this.currentCat))

      if (this.text == '') return goods
      return goods.filter((item) => {
        return item.title.indexOf(this.text) >= 0
      })
    },
    selectCategory(item){
      this.currentCat = item
    }
  },
  created() {
    this.getAllGoods()
    this.getAllCategories()
  }
}
</script>

<style>
</style>