// ==UserScript==
// @name         ShowEarnings
// @namespace    
// @version      1.2
// @description  show detailed information about sold images - keywords, decription, sales...
// @author       GG
// @match        http://submit.shutterstock.com/stats_date.mhtml*
// @match        https://submit.shutterstock.com/stats_date.mhtml*
// @copyright    2016, GG
// @require      http://code.jquery.com/jquery-latest.min.js
// @grant        none






// ==/UserScript==
/* jshint -W097 */
'use strict';


// =========== PARAMETERS ===========



//===================================

var $j = jQuery.noConflict();
var arrLocations = [];


$j(document).ready(function() {
    CreateStyles();    
    var opened=0;
    $j("a.link").each(function() {
        var ahref = this.href.match(/.*www.*id=(\d*)$/);
        if (ahref) {
            var href = ahref[1];
            $(this).removeAttribute("href");
            $(this).on("click","a.link", function() { 
                $j("div.gginfo").each(function() {
                    $(this).hide(); // style.display = "b";
                });
                GetImageData(href);
                $j("div.gginfo").each(function() {
                    $(this).hide(); // style.display = "b";
                });
                $j("div#"+href).each(function() {

                    if (opened == href) { $(this).hide(); opened = 0;}
                    else { $(this).show(); opened = href; };
                });
            } );
        }
    });
});


function GetImageData(imageID) {
    var div = document.createElement('div');
    div.id = imageID;
    div.className = "gginfo";
    div.style.display = "block";
    div.innerHTML = "<h4>Loading...</h4>";
    $j("div.shutterstock_submit_page").append(div);
    var d = new Date();
    var n = d.getTimezoneOffset() / 60;

    $j.ajax({
        url: window.location.protocol + '//submit.shutterstock.com/show_component.mhtml?component_path=mobile/comps/image_detail.mj&photo_id=' + imageID,
        type: "get",
        dataType: "html",
        cache: true,
        error: function (request, status, error) {
            console.log(request.responseText);
        },
        success: function( data ){
            var imageInfo = $j.parseJSON(data);

            if (imageInfo) {

                var  downloads = imageInfo.latest_downloads;
                var keywords = imageInfo.keywords;
                var totals = imageInfo.totals;
                var today = totals.today;
                var all = totals.all_time;
                var table;
                div.innerHTML = "<div class=\"closeDIV\">Close</div><br />";
                div.innerHTML += "<h4>Image statistics</h4>";
                div.innerHTML += "<a target=\"_new\" href=\"http://www.shutterstock.com/pic.mhtml?id=" + imageID + "\"><img align=\"right\" class=\"resize\" src=\"http:" + imageInfo.thumb_url + "\" /></a>";
                div.innerHTML += "<h5> " + imageInfo.description + "</h5>";
                div.innerHTML += "<b>Image ID:</b> <a target=\"_new\" href=\"http://www.shutterstock.com/pic.mhtml?id=" + imageID + "\">" + imageID + "</a><br />";
                //   var date = new Date ( ((arg.date_time/1000) + (6+n) * 3600)*1000).toUTCString().toLocaleString();
                console.log(imageInfo.uploaded_date);
                var uploaded = new Date( ((imageInfo.uploaded_date/1000) + (6+n) * 3600)*1000).toDateString();
                div.innerHTML += "<b>Uploaded on</b> " + uploaded  + "<br /><br />";
                div.innerHTML += "Earned <b>" + all.earnings + "$</b>";
                if (today.earnings > 0) {
                    div.innerHTML += " (<b>" + today.earnings + "$</b> today)";
                }
                div.innerHTML += "<br />";
                var s1 = (today.downloads) != 1 ? "s" : "";
                var s2 = (all.downloads) != 1 ? "s" : "";

                div.innerHTML += "Downloaded <b>" + all.downloads + " time" + s2;
                if (today.downloads > 0) {
                    div.innerHTML += " (<b>" + today.downloads + " time" + s1 + "</b> today)";
                }
                div.innerHTML += "<br />";
                var editURL = "http://submit.shutterstock.com/edit_media.mhtml?type=photos&approved=1&id=" + imageID;
                div.innerHTML += "<a href=\"" + editURL + "\" target=\"_new\">Edit title and keywords</a> (opens new window)<br/>";


                if (keywords.length > 0) {
                    div.innerHTML += "<br /><b>Keywords used to download image:</b><br />";
                    table = "<table  width=\"200px\">";
                    table += "<thead><th align=\"right\">Keyword</th><th align=\"right\">% used</th></thead><tbody>";
                    keywords.forEach(function(kw) { 
                        table += "<tr><td align=\"right\">" + kw.keyword + "</td><td align=\"right\">" + parseFloat(kw.percentage).toFixed(2) + "</td></tr>";
                    });
                    table += "</tbody></table>";
                    div.innerHTML += table;
                }
                else {
                    div.innerHTML += "<br /><b>No keyword info available.</b><br />";
                }

                if (downloads.length > 0) {
                    div.innerHTML += "<br /><b>Latest downloads</b> (max 20 or max last 365 days):</b><br />";
                    table = "<table  width=\"300px\">";
                    table += "<thead><th align=\"left\">Date</th><th align=\"right\">Earnings</th></thead><tbody>";
                    downloads.forEach(function(arg) { 
                        var date = new Date ( ((arg.date_time/1000) + (6+n) * 3600)*1000).toUTCString().toLocaleString();

                        table += "<tr><td align=\"left\">" + date + "</td><td align=\"right\">" + arg.amount + "</td></tr>";
                    });
                    table += "</tbody></table><br /";
                    div.innerHTML += table;
                }
                else
                {
                    div.innerHTML += "<br /><b>No downloads in the last 365 days.</b><br />";
                }
            }
            $j("div.closeDIV").on("click", function(){ $j(".gginfo").hide(); opened = 0;});
        }
    });
}

function CreateStyles() {
    var sheet = (function() {
        var style = document.createElement("style");
        style.appendChild(document.createTextNode(""));

        document.head.appendChild(style);
        return style.sheet;
    })(); 

    var resize = "max-width: 30%; max-height:30%;";
    var alink = "cursor: hand; cursor: pointer;";
    var closelink = "cursor: hand; cursor: pointer; text-decoration: underline;";
    var gginfo = "position: fixed; top: 60px; right: 10px; border:4px solid #eeeeee;" +
        "width: 330px; max-height: 70%;" + 
        "font-weight: normal;" +
        "resize: both;" +
        "padding: 10px 10px 10px 10px;" + 
        "background-color: white;" +
        "opacity: 1;" +
        "overflow: auto;" +

        "font-size: 12px;";
    addCSSRule(sheet, "div.gginfo", gginfo, 0);
    addCSSRule(sheet, "div.closeDIV", closelink, 0);
    addCSSRule(sheet, "a.link:hover", alink, 0);
    addCSSRule(sheet, "img.resize", resize, 0);
}

function addCSSRule(sheet, selector, rules, index) {
    if("insertRule" in sheet) {
        sheet.insertRule(selector + "{" + rules + "}", index);
    }
    else if("addRule" in sheet) {
        sheet.addRule(selector, rules, index);
    }
}
