# Taif-Baha
kolokoys-lottery





<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <meta name="msapplication-tap-highlight" content="no" />
    <meta name="viewport" content="user-scalable=no, initial-scale=1, maximum-scale=1, minimum-scale=1, 

width=device-width, height=device-height" /> 
    <style type="text/css">
        body{
            font-family: Arial, Helvetica, sans-serif;
            padding: 20px;            
        }
        input{
            padding: 8px 10px;
            width: 150px;
	    font-size: 25px;
            border-radius: 10px;
            border: solid 2px #190B07;
        }
        button{
            padding: 10px 15px;
            font-size: 15px;
	    font-weight: bold;
            background-color: #F7F8E0;
            border-radius: 10px;
            border: solid 2px #19070B;
            cursor: pointer;
        }
        button:hover{
            background-color: #F7F8E0;
        }

        .betdata td:first-child{
            font-weight: bold;
        }

        .betList th,
        .betList td{
            width: 300px;
            text-align: center;
        }

        .inputForm,
        .footer{
            padding: 5px;
            background: "H:\LOTTERY\Cars.jpg";
            border-radius: 10px;
        }
	
    </style>
    <title>
        Bet
    </title>
</head>
<body>
		
	<body background= "H:\LOTTERY\sky.jpg">

    <div>
        <div class="inputForm">
            <table style="margin: auto; font-size: 22px; max-width: 400px;">
                <thead>
                    <th>Input Number</th>
                    <th>Jackpot</th>
                    <th>Rumble</th>
                </thead>
                <tbody>
                    <tr>
                        <td>
                            <input id="num" type="number" maxlength="3" min="0" max="999">
                        </td>
                        <td>
                            <input id="Jackpot" type="number" maxlength="3" min="0" max="999">
                        </td>
                        <td>
                            <input id="Rumble" type="number" maxlength="3" min="0" max="999">
                        </td>

		                    </tr>
             <tr>
             <td> 
             </td>
             <td>                                                
             </td>
                <td style="text-align: center; padding: 25px 0;">
               	<button type="button" onclick="clearInputs()">Clear</button>     
               	<button type="button" onclick="onSubmit()">Save</button>   
                        

                        </td>
                    </tr>              
                </tbody>
            </table>   
        </div>   
        <table style="margin: auto; max-width: 750px; font-size: 20px; font-weight: bold; margin-top: 15px;" 

class="betList">
            <thead>
                <th>Number</th>
                <th>Count</th>
                <th>Jackpot</th>
                <th>Rumble</th>
                <th style="width: auto;"></th>
            </thead>
            <tbody >            
            </tbody>
        </table>    
        <div class="footer" style="margin-top: 100px;">
            <div style="max-width:400px; text-align:right; margin: auto;">
                    <button onclick="downloadCsv()">Download CSV</button>
                <button onclick="resetAll()">Reset All Data</button>
            </div>
        </div>
    </form>     
    <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
    <script>
        var dataList = [];
        if (typeof(Storage) !== "undefined") {
            const xdata = localStorage.getItem("bet") || "[]";
            dataList = JSON.parse(xdata);
        } else {
            alert("No Storage Support");
        }

        var clearInputs = function(){
            $("#num").val("");
            $("#Jackpot").val("");
            $("#Rumble").val("");
        }
 
        var onSubmit = function(){
            
            var num = $("#num").val().trim();
            var Jackpot = $("#Jackpot").val().trim();
            var Rumble = $("#Rumble").val().trim();

            if(!num || !Jackpot || !Rumble){
                alert("Invalid Entries");
                return;
            }

            var data = {
                num: num, Jackpot: parseInt(Jackpot), Rumble: parseInt(Rumble), count: 1
            };

            var matchFound = false;
            for(var i in dataList){
                if(dataList[i].num === num){
                    matchFound = true;
                    dataList[i].Jackpot += data.Jackpot;
                    dataList[i].Rumble += data.Rumble;
                    dataList[i].count += data.count;
                }
            }

            if(!matchFound){
                dataList.push(data);
            }

            if (typeof(Storage) !== "undefined") {
                localStorage.setItem("bet", JSON.stringify(dataList));
            } else {
                //alert("No Storage Support");
            }

            render();
        }

        var render = function(){
            $(".betList .betdata").remove();
            dataList.sort(function(a, b){
                if(a.num < b.num) return -1;
                if(a.num > b.num) return 1;
                return 0;
            });

            for(var i in dataList){
                var item = dataList[i];
                $(".betList tbody").append('<tr class="betdata" itemid="'+ item.num +'"><td>'+ item.num 

+'</td><td>'+ 
                item.count +'</td><td>'+ item.Jackpot +'</td><td>'+ item.Rumble +'</td><td><button 

onclick="deleteItem(this)">Delete</button></td></tr>');
            } 
        }

        var deleteItem = function(e){
            var id = $(e).closest("tr").attr("itemid");
            var match;
            for(var i in dataList){
                var item = dataList[i];
                if(item.num === id){
                    match = item;
                }
            } 
            if(match){
                var idx = dataList.indexOf(match);
                dataList.splice(idx, 1);
                localStorage.setItem("bet", JSON.stringify(dataList));
            }
            $(e).closest("tr").remove();
        }

        var resetAll = function(){
            dataList = [];
            localStorage.setItem("bet", JSON.stringify([]));
            render();
        }

        var downloadCsv = function(){
            var csvContent = "data:text/csv;charset=utf-8,";
            dataList.sort(function(a, b){
                if(a.num < b.num) return -1;
                if(a.num > b.num) return 1;
                return 0;
            });

            csvContent += "Number,Count,Jackpot,Rumble\r\n"; // add carriage return
            for(var i in dataList){
                var item = dataList[i];
                var arr = ["'"+ item.num, item.count, item.Jackpot, item.Rumble].join(",");
                csvContent += arr + "\r\n"; // add carriage return
            } 

            var encodedUri = encodeURI(csvContent);
            
            var link = document.createElement("a");
            link.setAttribute("href", encodedUri);
            link.setAttribute("download", "bet-data.csv");
            document.body.appendChild(link); // Required for FF
            link.click(); // This will download the data file named "my_data.csv".
            setTimeout(function(){
                $(link).remove();
            }, 1000);
        }

        render();
	
    </script>
</body>
</html>
