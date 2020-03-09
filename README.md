# sudoku
To see the result of my Sudoku in Vue you have to do this steps.

# Project setup ( Terminal )
a ) $ npm install -g @vue/cli
b ) $ vue create sudoku 

# After you create your project simply add the napa library
c ) npm install --save napa
d ) npm install

# Go to my folders at src/components/Sudoku.vue
Create your Sudoku.vyue file at components folder.
Copy all the code from me.

# Run the command npm run serve
npm run serve

## Also at your App.vue file clear HelloWorld And delete the assets folder because we gucci and we dont need it
## Import  your Sudoku file at your App.vue
<template>
  <div id="app">
    <Sudoku />
  </div>
</template>
<script>
import Sudoku from "./components/Sudoku.vue";
export default {
  name: 'App',
  components: {
   Sudoku
  }
}
</script>

# And there you Have your sudoku App
