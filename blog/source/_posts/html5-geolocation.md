### html5 geolocation

    if (navigator.geolocation){

        log("navigator.geolocation is supported");
        navigator.geolocation.getCurrentPosition(geocodePosition,onError, { timeout: 30000 });
        navigator.geolocation.watchPosition(watchGeocodePosition);
    }else{
        log("navigator.geolocation not supported");
    }

**getCurrentPosition 得到当前位置**

    geocodePosition: successCallback
    onError: errorCallbak
    opt{enableHighAccuracy: true, //是否精确计算，默认false
    maximumAge        : 30000, // property is a positive long value indicating the maximum age in milliseconds of a possible cached position that is acceptable to return.
    timeout           : 27000 //超时时间 default value is Infinity
    }

**watchPosition 跟踪位置变化**

    same as getCurrentPosition