# bustabit-gambling-bot
This script waits for low numbers (reds) streaks, then margintales. It also chases 19x anomalies.


    var crashes = 0;
    var winnings = 0;
    var myBet = 0;
    const MULTIPLIER = 2; // increase bet on loss by 2
    const CASH_OUT = 277; // PERCENTAGE
    const RED_TILL_START = 4; // After 4 crashes under 207(red) we start.
    const RED = 207;
    const BASE_BET = 100 // Base bet IN SATOSHIES!!! (100 sats = 1 bit)
    const CHASE_NINETEENS = false; // set this to true to enable chasing 19's every WAIT_19.
    const WAIT_19 = 80;
    var last_nineteen = 0;

    engine.on('game_starting', function(data) {
      if(crashes > RED_TILL_START) {
        console.log('Game in ' + data.time_till_start/1000 + ' sec. [ PLAYING ]')
      } else {
        console.log('NOT playing game in ' + data.time_till_start/1000 + ' secs. [ NOT PLAYING ]')
      }
      console.log('Last game ' + engine.lastGamePlay())
    });
    engine.on('game_crash', function(data) {
      if(CHASE_NINETEENS) {
        console.log("Last nineteen - " + last_nineteen);
        if(data.game_crash < 1900) last_nineteen++;
        else last_nineteen = 0;
        if(last_nineteen >= WAIT_19) {
          if(last_nineteen === WAIT_19) myBet = BASE_BET;
          else myBet = BASE_BET + 700 * (last_nineteen - WAIT_19);
          engine.placeBet(myBet, 1907, function(){ 
            console.log("CHASING NINETEEN! TRY NUMBER" + last_nineteen - WAIT_19 + "!")
            console.log("Betting " + myBet / 100 + " bits.");
          })
          return;
        }
      }
      console.log('Game crashed at ', data.game_crash);
      if(data.game_crash < RED) {
        crashes++;
        if(crashes >= RED_TILL_START && data.game_crash < CASH_OUT) winnings -= myBet / 100; 
        console.log("RED DETECTED: " + crashes + " OF " + RED_TILL_START + ".")
      } else {
        crashes = 0;
      }
      if(crashes >= RED_TILL_START) {
        // OLD CODE - THIS WAS MAKING ME MONEY!!!!
        // myBet = (crashes - RED_TILL_START - 1) * 2 * 500;
        // BUT CHANGED IT TO BE RIGHT ANYWAY
        if(crashes === RED_TILL_START) {
          myBet = BASE_BET
        } else { myBet = myBet * MULTIPLIER }
        engine.placeBet(myBet, CASH_OUT, function(){ 
          console.log("Betting " + myBet / 100 + " bits.");
        });
      }
    });
    engine.on('cashed_out', function(data) {
      if(data.username === engine.getUsername()) {
    1700 
        console.log('Cashed out ' + (myBet * data.stopped_at / 100) / 100 + ' bits!');
        winnings +=  (myBet * (data.stopped_at-100) / 100 ) / 100;
        console.log("Total winnings: " + winnings)
     }
    });

    engine.on('game_started', function(data) {
        if(data = data[engine.getUsername()]) {
          myBet = data.bet;
        }
    });

