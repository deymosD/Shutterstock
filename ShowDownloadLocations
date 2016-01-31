// ==UserScript==
// @name         DownloadLocations
// @namespace    
// @version      1.2
// @description  added detailed localization to Shutterstock Latest Downloads map
// @author       Satinka, GG update
// @match        http://submit.shutterstock.com/home.mhtml*
// @match        https://submit.shutterstock.com/home.mhtml*
// @copyright    2016, Satinka
// @require      http://code.jquery.com/jquery-latest.min.js
// @grant        none

// ==/UserScript==
/* jshint -W097 */
'use strict';

var showUnknownLocation = true;       // shows the title "Unknown location" in case coordinates are not defined (true), or empty title (false)
var useShortCountryName = true;       // US (true), or United States of America (false)


//===================================

var $j = jQuery.noConflict();


$j(document).ready(function() {
    CreateStyles();    
    GetCoords();

});

function GetCoords() {

    $j.ajax({
        url: window.location.protocol + '//submit.shutterstock.com/show_component.mhtml?component_path=download_map%2Frecent_downloads.mh',
        type: "get",
        dataType: "html",
        error: function (request, status, error) {
            //alert(request.responseText);
        },
        success: function( data ){
            var coords = $j.parseJSON(data);
            var div = document.createElement('div');
            div.className = "ggDL";
            div.style.display = "block";
            div.innerHTML = "<div class=\"refreshCoords\">Refresh</div>";

            div.innerHTML += "<H4>Earnings</h4>";

            $j.ajax({
                url: 'http://submit.shutterstock.com/show_component.mhtml?component_path=mobile/comps/earnings_overview.mj',
                type: "get",
                async: false,
                dataType: "html",
                error: function (request, status, error) {
                    console.log(request.responseText);
                },
                success: function( data ){
                    var res = $j.parseJSON(data);  
                    //{"last_7_days":"150.79","day":"1.90","lifetime":"27429.96","unpaid":"650.30"}
                    var zadnjih7 = 0;
                    if (res.last_7_days) { zadnjih7 = res.last_7_days; };
                    div.innerHTML += "Last 7 days: " + zadnjih7 + "$ <br />";
                    var danas = 0;
                    if (res.day) { danas = res.day; };
                    div.innerHTML += "Today: " + danas + "$ <br />";
           //         div.innerHTML += "Lifetime: " + res.lifetime + "$ <br />";
           //         div.innerHTML += "Unpaid: " + res.unpaid + "$ <br />";
                }  
            }); 


            div.innerHTML += "<h4>Download locations</h4>";
            $j("div.shutterstock_submit_page").append(div);


            $j.each(coords, function( ind, el ) {
                var id = el.media_id;
                var img = el.thumb_url;
                var time = el.time;
                var loc;

                if (el.latitude !== null && el.longitude !== null) {
                    $j.ajax({
                        url: 'https://maps.googleapis.com/maps/api/geocode/json?latlng=' + el.latitude + ',' + el.longitude,
                        type: "get",
                        async: false,
                        dataType: "html",
                        error: function (request, status, error) {
                            console.log(request.responseText);
                        },
                        success: function( data ){
                            var res = $j.parseJSON(data);

                            if (res.status == "OK") {
                                loc = ExtractLocation(res.results[0].address_components);

                            }

                        }  
                    }); 

                }
                else {
                    loc = showUnknownLocation ? "Unknown location" : "";
                }


                div.innerHTML += "<a target=\"_new\" href=\"http://www.shutterstock.com/pic.mhtml?id=" + id + "\"><img src=\"" + img + "\" /></a><br />";

                if (el.latitude  && el.longitude ) {
                    div.innerHTML +=  "<a target=\"_new\" href=\"https://www.google.hr/maps/place/" + el.latitude + "+"+ el.longitude + "\">" + loc + "</a><br />";
                }
                else {
                    div.innerHTML += "Unknown, middle of Atlantic :)<br />"; 
                }
                var d = new Date();
                var n = d.getTimezoneOffset() / 60;

                var t = new Date((time + (6+n) * 3600) *1000).toTimeString().split(" ")[0];
                div.innerHTML += "Time: " + t + "<hr />";
            });
            $j("div.refreshCoords").on("click", function(){ div.innerHTML="<h4>Refreshing...</h4>"; GetCoords(); });
        }
    });

}



function ExtractLocation(details) {
    var loc = "";
    var country = "";
    var locality = "";
    var admin_area1 = "";
    var admin_area2 = "";

    $j.each(details, function( ind, el ) {
        if ($j.inArray("country", el.types) != -1) 
            country = useShortCountryName ? el.short_name : el.long_name;
        if ($j.inArray("locality", el.types) != -1) 
            locality = el.long_name;
        if ($j.inArray("administrative_area_level_1", el.types) != -1) 
            admin_area1 = el.short_name;
        if ($j.inArray("administrative_area_level_2", el.types) != -1) 
            admin_area2 = el.long_name;
    });
    loc = loc + ((locality !== "") ? locality + ", " : "") +
        ((admin_area2 != "") ? admin_area2 + ", " : "") +
        ((admin_area1 != "") ? admin_area1 + ", " : "") +
        ((country !== "") ? country : "");
    return loc;
}



function CreateStyles() {
    var sheet = (function() {
        var style = document.createElement("style");
        style.appendChild(document.createTextNode(""));

        document.head.appendChild(style);
        return style.sheet;
    })(); 
    var refreshCoords = "cursor: hand; cursor: pointer; text-decoration: underline;";
    var ggDL = "position: fixed; top: 60px; left: 50px; width: 200px; height: 95%; overflow: auto;" +
        "border: 1px solid #eeeeee; background-color: white; resize: both;" +
        "font-size: 11px;" +
        "text-shadow: 0 0 5px #fff; text-align: left;";
    addCSSRule(sheet, ".ggDL", ggDL, 0);
    addCSSRule(sheet, "div.refreshCoords", refreshCoords, 0);
}

function addCSSRule(sheet, selector, rules, index) {
    if("insertRule" in sheet) {
        sheet.insertRule(selector + "{" + rules + "}", index);
    }
    else if("addRule" in sheet) {
        sheet.addRule(selector, rules, index);
    }
}

function sortByKey(array, key) {
    return array.sort(function(a, b) {
        var x = a[key]; var y = b[key];
        return ((x < y) ? -1 : ((x > y) ? 1 : 0));
    });
}
