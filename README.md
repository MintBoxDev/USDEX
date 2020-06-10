
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css" integrity="sha384-9aIt2nRpC12Uk9gS9baDl411NQApFmC26EwAOH8WgZl5MYYxFfc+NcPb1dKGj7Sk" crossorigin="anonymous">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/crypto-js/3.1.9-1/crypto-js.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/crypto-js/3.1.9-1/hmac-sha256.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/crypto-js/3.1.9-1/enc-base64.min.js"></script>


  <div class="row mt-5">
    <div class="col col-12 text-center">
      <h1>Total coins issued: <span id="totalIssued" class="text-info"></span>USDEX</h1>
      <h1>Backed USDT balance: ₮<span class="" id="usdtBalance"></span></h1>
    </div>
    <div class="col col-12 text-center">
      <div class="small">This website is provided for public audit of USDEX coin and running directly from public GitHub repository</div>
      <div class="small">USDT balance is taken directly from <a href="https://www.bithumb.pro/" target="_blank">Bithumb Global</a> cryptocurrency exchange used to back USDEX coin. USDEX balance is taken from official <a href="https://explorer.minter.network/address/Mx114c9906b54b44895b1c4d2336abb54b687e5ace">wallet</a> </div>
    </div>
  </div>

  <script>
    //create and sign Bithumb request
    const key="e48fe9afff85e5bd6e349a2fd5dcaabd";
    const secret="1362b07706254f386e93a803a768606ddfe0f161967429f7135a60f113796453"
    const ts = Date.now();
    const hash = CryptoJS.HmacSHA256("apiKey="+key+"&assetType=spot&coinType=USDT&timestamp="+ts+"&version=v1.0.0", secret);

    let xhttp = new XMLHttpRequest();
    xhttp.open("POST", "https://global-openapi.bithumb.pro/openapi/v1/spot/assetList?coinType=USDT&assetType=spot", false);
    xhttp.setRequestHeader("Content-type", "application/json; charset=utf-8");
    xhttp.send('{"apiKey":"'+key+'","assetType":"spot","coinType":"USDT","signature":"'+hash+'","timestamp":"'+ts+'","version":"v1.0.0"}');
    let responseText = JSON.parse(xhttp.responseText);
    const USDTBalance = responseText.data[0].count;




    //Get balance of stable coin wallet
    const stableSupply = 100000;
    const stableWallet = "Mx114c9906b54b44895b1c4d2336abb54b687e5ace";
    const stableTicker = "MINTUSD";
    xhttp = new XMLHttpRequest();
    xhttp.open("GET", "https://explorer-api.minter.network/api/v1/addresses/"+stableWallet, false);
    xhttp.setRequestHeader("Content-type", "application/json; charset=utf-8");
    xhttp.send();
    responseText = JSON.parse(xhttp.responseText);
    var stableAmount = responseText.data.balances.find(c=> c.coin === stableTicker).amount;
    var bipAmount = responseText.data.balances.find(c=> c.coin === 'BIP').amount;

    var stableEmission = stableSupply - stableAmount;


    let cls = stableEmission > USDTBalance ? "text-danger": "text-success";

    document.getElementById('usdtBalance').innerText = Number(USDTBalance).toFixed(2);
    document.getElementById('usdtBalance').className = cls;
    document.getElementById('totalIssued').innerText = Number(stableEmission).toFixed(2);

  </script>

