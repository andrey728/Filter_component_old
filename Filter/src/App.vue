
<template>
  <header>
  </header>

  <div class="Main">


    <div>
      <textarea class="input_json" cols="35" rows="35" v-model="some_json" placeholder="add JSON"></textarea>
      <button class="AddButton" @click="JSON_import"> <span class="MainText">json import</span> </button>
    </div>




    <div class="Panel">
    <button class="AddButton" @click="AddClick"> <span class="MainText">Add</span> </button>
      <button class="RecuestButton" @click="Request"> <span class="MainText">Request</span> </button>
    <select class="choise" v-model="selected" @change="choise">
      <option disabled value=""> Please select one </option>
      <option>Number</option>
      <option>String</option>
      <option>Bool</option>
    </select>
  </div>


    <div class="FilterCraft" id = 'n'>

    </div>


    <div class="visible" id='vis_1'></div>

  </div>

  <main></main>
</template>


//___________________________

<script setup>
import { ref } from 'vue'

let type = ref({
  Num: false,
  Str: false,
  Bool: false
})

let Request_arrey = []


const selected = ref('')

const RequestContent = ref({
  Code: null,
  Caption: null,
  Type: selected.value,
  Like: null,
  GT: null,
  LT: null,
  GTE: null,
  LTE: null,
  EQ: null,
  NEQ: null
})

const RequestContent_for_num = ref({
  Code: null,
  Caption: null,
  Type: "Number",
  Values: {
    GT: null,
    LT: null,
    GTE: null,
    LTE: null,
    EQ: null,
    NEQ: null
  }
})

const RequestContent_for_str = ref({
  Code: null,
  Caption: null,
  Type: "String",
  Values: {
    Like: null,
    EQ: null,
    NEQ: null
  }
})

const RequestContent_for_bool = ref({
  Code: null,
  Caption: null,
  Type: "Bool",
  Values: {
    EQ: null
  }
})

let file = new Blob()

let json_obj;



//___________TEST JSON INPUT______________
const some_json = ref('')

function JSON_import() {

  var json = JSON.parse(some_json.value);


  function createDiv(className, N, type){
    var div = document.createElement(type);
    div.className = className;
    div.id = className + "_" + N
    return div;
  }

  function take_div(id, obj){
    var header = obj.caption
    document.getElementById(id).innerHTML += header;
  }

  function createOne(Card, def, obj){
    var div = document.createElement("div");
    var span = document.createElement("span");
    var input = document.createElement("input");
    span.className = "MainText"
    span.innerHTML += def
    input.className = "cherecters"
    input.placeholder = def
    RequestContent_for_num.code = input.innerHTML
    var ti = obj
    input.addEventListener("change",function hh (inpt) {


      console.log(inpt.composedPath())
      var num_of_card = parseFloat(inpt.composedPath()[2].id.replace('Card_', ""))
      console.log(inpt.composedPath()[2].id)
      console.log(num_of_card)
      obj = inpt.composedPath()[0].value

      if (def === "????????????") Request_arrey[num_of_card].value.Values.GT = inpt.composedPath()[0].value
      if (def === "????????????") Request_arrey[num_of_card].value.Values.LT = inpt.composedPath()[0].value
      if (def === "??????????") Request_arrey[num_of_card].value.Values.EQ = inpt.composedPath()[0].value
      if (def === "???? ??????????") Request_arrey[num_of_card].value.Values.NEQ = inpt.composedPath()[0].value
      if (def === "????????????????") Request_arrey[num_of_card].value.Values.Like = inpt.composedPath()[0].value
      if (def === "???? ????????????????") Request_arrey[num_of_card].value.Values.NEQ = inpt.composedPath()[0].value
      if (def === "??????????-???? ??????????????????????????") Request_arrey[num_of_card].value.Values.GT = inpt.composedPath()[0].value
    })

    div.appendChild(span)
    div.appendChild(input)

    console.log("??????????????")


    Card.appendChild(div)


  }

  function Feel_Filter_card(Card, obj){

    if(obj.type === "number"){
      Request_arrey.push(RequestContent_for_num)
      console.log("1")
      createOne(Card,'????????????', RequestContent_for_num.value.Values.GT)
      createOne(Card,'????????????')
      createOne(Card,'??????????')
      createOne(Card,'???? ??????????')
    }
    if(obj.type === "string"){
      Request_arrey.push(RequestContent_for_str)
      console.log("2")
      createOne(Card,'????????????????')
      createOne(Card,'???? ????????????????')
    }
    if(obj.type === "bool"){
      Request_arrey.push(RequestContent_for_bool)
      console.log("3")
      createOne(Card,'??????????-???? ??????????????????????????')
    }
  console.log("hjvf gblh")
  }


  for (var i = 0; i < json.length; i++) {


    document.getElementById('n').appendChild(createDiv('Filter_Card', i, 'div'));
    document.getElementById('Filter_Card_{i}'.replace('{i}', i)).appendChild(createDiv('MainText', i, 'span'));
    take_div('MainText_{i}'.replace('{i}', i), json[i])

    document.getElementById('Filter_Card_{i}'.replace('{i}', i)).appendChild(createDiv('PlussButton', i, 'button'));
    document.getElementById('PlussButton_{i}'.replace('{i}', i)).innerHTML += "???????????????????? v";


    document.getElementById('Filter_Card_{i}'.replace('{i}', i)).appendChild(createDiv('Card', i, "div"));
    Feel_Filter_card(document.getElementById('Card_{i}'.replace('{i}', i)), json[i])


    document.getElementById('PlussButton_{i}'.replace('{i}', i)).addEventListener('click', function(button){
      var button_id = button.composedPath()[0].id
      var this_card = document.getElementById(button_id.replace('PlussButton_', "Card_"))
      console.log(button.composedPath()[0].innerHTML)
      console.log(this_card.style)
      if (this_card.style.display === "" || this_card.style.display === "none") {
        this_card.style.display = "flex"
        button.composedPath()[0].innerHTML = '???????????????? ^'
      } else {
        this_card.style.display = "none"
        button.composedPath()[0].innerHTML = '???????????????????? v'
      }
    });
  }

console.log(Request_arrey)





}


//___________TEST JSON INPUT______________




function choise() {
  type.value.Num = (selected.value === "Number")
  type.value.Str = (selected.value === "String")
  type.value.Bool = (selected.value==="Bool")
}

function AddClick() {
  for (var i = 0; i < Request_arrey.length; i++) {
    create_requet(Request_arrey[i])
  }
}

function create_requet (content){
  RequestContent.value.Type = selected.value

  json_obj = JSON.stringify(content.value, (key, value) =>
  {
    if (value !== null && value !== "") return value
    else if (key === "Value") return key.value
  })
  console.log(content)
  console.log(json_obj)

  ////// try to display
  var data = JSON.parse(json_obj);

  console.log(data)
  var header = '<h3>Name for User: ' + data.Caption + '</h3>';
  var list = '';
  for (var i in data) {
    if(typeof data[i] === "object"){
      console.log(data[i])
      list += '<li>' + i + ': ' + '\r\n' + '[' + '\r\n';
      for (var j in data[i]){
        console.log(j)
        console.log(data[i][j])
        list += "  " + j + ': ' + data[i][j] + '\r\n';
      }
      list += ']' + '\r\n' + '</li>';
    } else {
      list += '<li>' + i + ': ' + data[i] + '</li>';
    }
  }



  document.getElementById('vis_1').innerHTML += header;
  document.getElementById('vis_1').innerHTML +=  list;



  // ?????????????? ????????
  if (file.type==='application/json'){
    file = new Blob(
        // ?????????????????????? ????????????
        [file,",", JSON.stringify(data)], {
          type: 'application/json'
        }
    )
  }
  else {
    file = new Blob(
        // ?????????????????????? ????????????
        [JSON.stringify(data)], {
          type: 'application/json'
        }
    )
  }

}


function Request() {
  file = new Blob(
      // ?????????????????????? ????????????
      ["[",file,"]"], {
        type: 'application/json'
      }
  )

  // ?????????????? ?????????????? "a"
  const link = document.createElement('a')
  // ?????????????????????? ?????????????? "href" ???????? "a" ?? ???????????????????? ??????????
  link.setAttribute('href', URL.createObjectURL(file))
  // ?????????????? "download" ?????????????????? ?????????????????? ??????????, ???? ?????????????? ?????????????????? ????????????
  // ???????????????? ?????????? ???????????????? - ???????????????? ???????????????????????? ??????????
  link.setAttribute('download', 'data.json')
  // ?????????????????? ???????????????????? ????????????
  link.textContent = 'DOWNLOAD DATA'
  // ???????????????? ?????????????? ?? ?????????????????? ?? ?????????????? "main"
  document.querySelector('.visible').append(link)
  // ?????????????? ???????????? ???? ????????
  URL.revokeObjectURL(file)
}

</script>


<style>
@import './assets/base.css';

#app {
  max-width: 1280px;
  margin: 0 auto;
  padding: 2rem;

  font-weight: normal;
}

header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

a,
.green {
  text-decoration: none;
  color: hsla(160, 100%, 37%, 1);
  transition: 0.4s;
}

@media (hover: hover) {
  a:hover {
    background-color: hsla(160, 100%, 37%, 0.2);
  }
}

@media (min-width: 1024px) {
  body {
    display: flex;
    place-items: center;
  }

  #app {
    display: grid;
    grid-template-columns: 1fr 1fr;
    padding: 0 2rem;
  }



  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }

  .logo {
    margin: 0 2rem 0 0;
  }
}
</style>
