<template>
  <div>
    <h1 class="text-center dark-green-text padding-top-20">
      <img src="@/assets/logo.png" height="32" width="32" />
      Lucky Lottery
      <img src="@/assets/logo.png" height="32" width="32" />
    </h1>
    <p class="text-center green-text">
      Enter your combination to see if you've won
      <br/>
      <small>
        <em>You will be sent to a "You Won" page</em>
      </small>
    </p>
    <div id="lock-plate">
      <span></span><span></span><span></span><span></span>
      <div class="bar"></div>
      <div id="lock-wrapper">
        <div class="welcome-message">WINNER!</div>
        <div class="lock-dial" id="dial-one"><ul data-combo-num="0"><li>5</li><li>6</li><li>7</li><li>8</li><li>9</li><li>0</li><li>1</li><li>2</li><li>3</li><li>4</li></ul></div>
        <div class="lock-dial" id="dial-two"><ul data-combo-num="0"><li>5</li><li>6</li><li>7</li><li>8</li><li>9</li><li>0</li><li>1</li><li>2</li><li>3</li><li>4</li></ul></div>
        <div class="lock-dial" id="dial-three"><ul data-combo-num="0"><li>5</li><li>6</li><li>7</li><li>8</li><li>9</li><li>0</li><li>1</li><li>2</li><li>3</li><li>4</li></ul></div>
        <div class="lock-dial" id="dial-four"><ul data-combo-num="0"><li>5</li><li>6</li><li>7</li><li>8</li><li>9</li><li>0</li><li>1</li><li>2</li><li>3</li><li>4</li></ul></div>
        <div class="lock-dial" id="dial-five"><ul data-combo-num="0"><li>5</li><li>6</li><li>7</li><li>8</li><li>9</li><li>0</li><li>1</li><li>2</li><li>3</li><li>4</li></ul></div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {},
  data: () => ({
  }),
  mounted: function () {
    const self = this;
    var comboArray = [0, 0, 0, 0, 0];
    var combination = [1, 0, 0, 0, 1];
    
    var gridIncrement = $( ".lock-dial ul" ).css('line-height').replace('px', '')/2;
    var numNums = $( ".lock-dial:eq(0) ul li" ).length;
    var halfHeight = gridIncrement*numNums;
    var initTop = -(halfHeight-gridIncrement);
    
    $( ".lock-dial ul" ).css('top', initTop);
    
    $( ".lock-dial ul" ).draggable({
      grid: [ 0, gridIncrement ],
      axis: 'y',
      drag: function(){
        var dragDir = $(this).css('top').replace('px', '') < initTop ? "up" : "down";
        
        if(dragDir == "up"){
          var curNum = parseInt($(this).find('li:last-child').text()) + 1;
          if(curNum < 10){
            $(this).append('<li>'+curNum+'</li>');
          }else{
            $(this).append('<li>0</li>');
          };
        }else{
          var curNum = parseInt($(this).find('li:first-child').text()) - 1;
          var thisTop = parseInt($(this).css('margin-top').replace('px', ''));

          $(this).css({
            marginTop: thisTop-(gridIncrement*2)
          });

          if(curNum > -1){
            $(this).prepend('<li>'+curNum+'</li>');
          }else{
            $(this).prepend('<li>9</li>');
          };
        };
      },
      stop: function(){
      
        //MATHS		
        var negOrPos = $(this).css('margin-top').replace('px', '') > 0 ? 1 : -1;
        var thisTopTotal = parseInt($(this).css('top').replace('px', '')) + Math.abs(initTop);
        var marginMinified = parseInt(Math.abs($(this).css('margin-top').replace('px', ''))) - thisTopTotal;
        var numIncs = Math.floor(marginMinified/(halfHeight*2));
        var totalDif = numIncs*(halfHeight*2);
        var topTen = (marginMinified - totalDif)*negOrPos;
        var activeIndex = Math.abs(topTen/(gridIncrement*2)) + (halfHeight/(gridIncrement*2));
        
        $(this).attr("data-combo-num", $(this).find('li').eq(activeIndex).text()).css({
          top: -315,
          marginTop: topTen
        }).find('li').slice(20).remove();
        
        for(var i=0; i<$( ".lock-dial ul" ).length; i++){
          comboArray[i] = $( ".lock-dial ul:eq("+i+")" ).attr("data-combo-num");
        }
        
        
        if(comboArray == ""+combination){
          $('.lock-dial ul').draggable('disable');
          $('#lock-wrapper').addClass("unlocked");
          $('.lock-dial').each(function(){
            var $this = $(this);
            $this.find('ul').delay(400).css('color', '#0f0').fadeOut(function(){
              $('.welcome-message').fadeIn();
              $this.animate({
                marginTop: 150
              }, function(){
                $this.fadeOut(function(){
                  self.$router.push('winner');
                });
              });
            });
          });
        }
      }
    });
  },
  computed: {},
  methods: {
    updateMe() {
      console.log('called');
      this.$forceUpdate();
    }
  }
}
</script>

<style>
  @import url(https://fonts.googleapis.com/css?family=Libre+Baskerville:700);

  .padding-top-20 {
    padding-top: 40px;
  }
  
  .dark-green-text {
    color: #3C7113;
  }
  
  .green-text {
    color: #214B00;
  }

  body{
    font-family: 'Libre Baskerville', serif;
    background-color: #A2FF93;
    /* background-image: linear-gradient(-45deg, #fff 25%, rgba(0, 0, 0, 0) 25%, rgba(0, 0, 0, 0) 50%, #fff 50%, #fff 75%, rgba(0, 0, 0, 0) 75%, rgba(0, 0, 0, 0));
    -webkit-background-size: 4px 4px;
    -moz-background-size: 4px 4px;
    background-size: 4px 4px; */
    color: #999;
  }

  #lock-plate{
    padding: 30px 0;
    border: 1px solid #cfd3d6;
    background: #ccc;
    text-align: center;
    width: 486px;
    background: -webkit-linear-gradient(top, #eee 0%,#949ba0 100%);
    background: linear-gradient(to bottom, #eee 0%,#949ba0 100%);
    border-radius: 4px;
    margin: 50px auto;
    box-shadow: 0 1px 0 rgba(0, 0, 0, 0.6);
    position: relative;
  }

  #lock-plate span{
    display: block;
    position: absolute;
    width: 15px;
    height: 15px;
    border-radius: 10px;
    box-shadow: inset 0 1px 0 rgba(0, 0, 0, 0.6), inset 0 -1px 0 rgba(255, 255, 255, 0.8);
    background: #bdc1c4;
  }

  #lock-plate span:first-child{
    top: 9px;
    left: 9px;
  }

  #lock-plate span:nth-child(2){
    top: 9px;
    right: 9px;
  }

  #lock-plate span:nth-child(3){
    bottom: 9px;
    left: 9px;
  }

  #lock-plate span:nth-child(4){
    bottom: 9px;
    right: 9px;
  }

  .bar {
    display: block;
    position: absolute;
    width: 488px;
    height: 8px;
    /* box-shadow: inset 0 1px 0 rgba(241, 96, 96, 0.6), inset 0 -1px 0 rgba(241, 96, 96, 0.8); */
    background-color: rgba(241, 96, 96, 0.8);
    top: 92px;
    left: 0;
  }


  #lock-wrapper{
    width: 396px;
    padding: 0 20px 0 8px;
    border-radius: 7px;
    height: 120px;
    border: 1px solid rgba(255, 255, 255, 0.6);
    box-shadow: inset 0 0 15px rgba(0, 0, 0, 0.9);
    background: -webkit-linear-gradient(top, #c4c4c4 0%,#676767 100%);
    background: linear-gradient(to bottom, #c4c4c4 0%,#676767 100%);
    overflow: hidden;
    margin: 0 auto;
  }

  .welcome-message{
    position: absolute;
    top: 50%;
    left: 50%;
    height: 90px;
    width: 400px;
    margin-top: -40px;
    margin-left: -200px;
    font-size: 70px;
    font-weight: bold;
    color: #FFF;
    font-family: Arial, Helvetica, sans-serif;
    display: none;
  }

  .lock-dial{
    height: 120px;
    width: 68px;
    margin-left: 11px;
    background: -webkit-linear-gradient(top, #8c9093 0%,#b6babd 9%,#ccd2d6 18%,#ffffff 55%,#ccd2d6 82%,#b6babd 91%,#8c9093 100%);
    background: linear-gradient(to bottom, #8c9093 0%,#b6babd 9%,#ccd2d6 18%,#ffffff 55%,#ccd2d6 82%,#b6babd 91%,#8c9093 100%);
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.8);
    float: left;
    position: relative;
    cursor: move;
  }

  #lock-wrapper .lock-dial ul{
    margin: 0;
    padding: 0;
    text-align: center;
    list-style: none;
    font-size: 53px;
    line-height: 70px;
    color: #414f6b;
    text-shadow: 0 -2px 0 #212c42;
    -webkit-user-select: none;  /* Chrome all / Safari all */
    -moz-user-select: none;     /* Firefox all */
    -ms-user-select: none;
    transition: box-shadow .45s linear, color .25s linear;
    -webkit-transition: box-shadow .45s linear, color .25s linear;
  }

  #lock-wrapper.unlocked .lock-dial ul{
    color: green;
    text-shadow: 0 0 5px #3f3;
  }

  .text-center {
    text-align: center;
  }
</style>
