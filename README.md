<code>
  $(document).ready(function(){
    function livedata(){
        countCampre();
        var getCompareData = localStorage.getItem('compare-listing');
        if(getCompareData != null && getCompareData != '[]'){
            getCompareData = JSON.parse(getCompareData);
            getCompareData.forEach(function(data,key){
                addtocompare(data.id,data.url,data.img_path,data.title,data.carCategory);
                $(".common-campare-btn").each(function() {
                    $commonCampareBtn = $(this).attr("data-listingId");
                    if($commonCampareBtn == data.id)
                    {
                        $(this).addClass("remove-compare");
                        $(this).removeClass("addToCompare");
                        $(".remove-compare").html(`<span class="text-danger d-flex justify-content-center align-items-center"><span class="material-icons mr-1">remove_circle</span> Remove</span>`);
                    }
                });
            });  
        }
        else{
            checkListingAvailable();
        }
    }

    livedata();

    $(".compare-fixed-icon").click(function(){
        $checkAttr = $(".compare-panel").attr("data-panel");
        if($checkAttr == 'hide')
        {
            $(".compare-panel").css({"right":"0px"});
            $(".compare-panel").attr("data-panel","show");
            $("body").addClass("open-panel");
            $(".overlay-div").addClass("active");
        }
        else{
            $(".compare-panel").css({"right":"-378px"});
            $(".compare-panel").attr("data-panel","hide");
            $("body").removeClass("open-panel");
            $(".overlay-div").removeClass("active");
            livedata();
        }
    });
    $(document).on("click",".addToCompare",function(){
        $checkAttr = $(".compare-panel").attr("data-panel");
        $listingCollection = $(this).attr('data-collection');
        
        $(this).addClass("remove-compare");
        $(this).removeClass("addToCompare");
        $(".remove-compare").html(`<span class="text-danger d-flex justify-content-center align-items-center"><span class="material-icons mr-1">remove_circle</span> Remove</span>`);
        let [id,url,img_path,title,carCategory] =  $listingCollection.split('%%');
        StoreDataLocalhost(id,url,img_path,title,carCategory);
        if($checkAttr == 'hide')
        {
            $(".compare-panel").css({"right":"0px"});
            $(".compare-panel").attr("data-panel","show");
            $("body").addClass("open-panel");
            $(".overlay-div").addClass("active");
        }
        else{
            $(".compare-panel").css({"right":"-378px"});
            $(".compare-panel").attr("data-panel","hide");
            $("body").removeClass("open-panel");
            $(".overlay-div").removeClass("active");
           
        }
    });
    $(".panel-close,.overlay-div").click(function(){
        $(".compare-panel").css({"right":"-378px"});
        $(".compare-panel").attr("data-panel","hide");
        $("body").removeClass("open-panel");
        $(".overlay-div").removeClass("active");
    });
 
    $(document).on("click",".remove-compare-listing",function(){
        $ListingId = $(this).attr("data-id");
        $(this).parent().parent().remove();
        removeCompare($ListingId);
        checkListingAvailable();
        $(".common-campare-btn").each(function() {
            $commonCampareBtn = $(this).attr("data-listingId");
            if($commonCampareBtn == $ListingId)
            {
                $(this).addClass("addToCompare");
                $(this).removeClass("remove-compare");
                $(this).html(`<span class="material-icons mr-1">compare_arrows</span>Add to Compare`);
            }
        });
        
        let remove_btn = $(".remove-compare");
        remove_btn.addClass("addToCompare");
        remove_btn.removeClass("remove-compare");
        remove_btn.html(`Compare`);
        
    });
    
    function addtocompare(id,url,img_path,title,carCategory){
      $(".compare-body").append(`<div class="media mb-3 compare-listing"><input type="hidden" name="listing_id[]" value="${id}"><a href="${url}"><img src="${img_path}" alt="image" style="height:100px;" class="mr-2"/></a><div class="media-body"><a href="${url}"><h6 class="mt-0">${title}</h6></a><p>${carCategory}</p><p class="text-danger remove-compare-listing" data-id="${id}">Remove</p></div></div> `);
      checkListingAvailable();
      countCampre();
    }

    function checkListingAvailable(){
        var check_compare = $(".compare-body").html();
        $length = $(".compare-body .compare-listing").length;
        if($length != 1)
        {
            $(".compare-footer").html(`<button class="btn btn-primary compare-btn" type="submit">Compare</button>`);
        }
        else{
            $(".compare-footer").html('');
        }
        if(check_compare.trim() == '')
        {
            $(".compare-body").append(`<div class="removeNotListing mb-3"><h6>There are no listings to compare</h6></div>`);
            $(".compare-footer").html('');
            $length = $(".compare-body .compare-listing").length;
        }
        else{
            $(".compare-body").find(".removeNotListing").remove();
          
        }
        countCampre();
    }
    function StoreDataLocalhost(id,url,img_path,title,carCategory){
        var dataList = [];
        var data ={
            id: id,
            url: url,
            img_path: img_path,
            title: title,
            carCategory: carCategory
        };
        dataList.push(data);
        dataList = dataList.concat(JSON.parse(localStorage.getItem('compare-listing')||'[]'));
        localStorage.setItem("compare-listing", JSON.stringify(dataList));
        addtocompare(id,url,img_path,title,carCategory);
    }
    function removeCompare(ListingId){
        var getCompareData = localStorage.getItem('compare-listing');
        getCompareData = JSON.parse(getCompareData);
        const removeById = (arr, id) => {
            const requiredIndex = arr.findIndex(el => {
               return el.id === String(id);
            });
            if(requiredIndex === -1){
               return false;
            };
            return !!arr.splice(requiredIndex, 1);
         };
        removeById(getCompareData, ListingId);
       localStorage.setItem("compare-listing", JSON.stringify(getCompareData)); 
    }
    $(document).on("click",".remove-compare",function(){
        $(".compare-body").html('');
        $ListingId = $(this).attr("data-listingid");
        removeCompare($ListingId);
        $(this).addClass("addToCompare");
        $(this).removeClass("remove-compare");
        $(this).html(`<span class="material-icons mr-1">compare_arrows</span>Add to Compare`);
        var getCompareData = localStorage.getItem('compare-listing');
        if(getCompareData != null && getCompareData != '[]'){
            getCompareData = JSON.parse(getCompareData);
            getCompareData.forEach(function(data,key){
                addtocompare(data.id,data.url,data.img_path,data.title,data.carCategory);
            });  
        }
        else{
            checkListingAvailable();
        }
    });
});

function countCampre(){
    var getCompareData = localStorage.getItem('compare-listing');
    if(getCompareData != null)
    {
        getCompareData = JSON.parse(getCompareData);
        if(getCompareData.length <= 0 && getCompareData.length == ''){
            $(".counting-main-div").html('');
        }
        else{
            $(".counting-main-div").html(`<div class="count-campare">${getCompareData.length}</div>`);
        }
    }
}

</code>
