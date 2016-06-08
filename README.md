# Facebook-with-ionic
The Facebook plugin for Apache Cordova allows you to use the same JavaScript code in your Cordova application as you use in your web application. However, unlike in the browser, the Cordova application will use the native Facebook app to perform Single Sign On for the user. If this is not possible then the sign on will degrade gracefully using the standard dialog based authentication.
To work facebook you neded to install plugin first

Cordova-plugin-facebook4
https://github.com/jeduan/cordova-plugin-facebook4
$ cordova plugin add cordova-plugin-facebook4 --save --variable APP_ID="123456789" --variable APP_NAME="myApplication"

For example you start coding in this way:-


$scope.facebookSignIn = function() {
    $ionicLoading.show({
      //template: 'Logging in...'
    });
    window.facebookConnectPlugin.login(["public_profile","email"],function (response) {
       //alert(JSON.stringify(response));
        if (response.authResponse) {
             fbLoginSuccess(response.authResponse.accessToken);
            } else {
               alert("Login Failed");
            }
    })

}

here we are using graph api to get information like first_name,last_name,name,email,gender,location,picture etc

var fbLoginSuccess = function (access_token) {
    
    $http.get("https://graph.facebook.com/v2.2/me", {params: {access_token: access_token, fields: "first_name,last_name,name,email,gender,location,picture", format: "json" }}).then(function(result) {
        //alert(JSON.stringify(result.data));
         trisFbLogin(access_token,result.data);
    }, function(error) {
        alert("Error: " + error);
    });
}  

here we are using our API to get login in our server 

var trisFbLogin = function (access_token,fbdata) {
   
    if(window.localStorage.getItem("mobileInfo") !== undefined) {
        $scope.token = JSON.parse(window.localStorage.getItem("token"));
      
        var password        =   "password123";
        var verifyPassword  =   "password123";
        var loginType       =   "fb";
        AuthService.fblogin({
                                first_name      :   fbdata.first_name,
                                last_name       :   fbdata.last_name,
                                email           :   fbdata.email,
                                nickname        :   fbdata.first_name,
                                username        :   fbdata.email,
                                avatar          :   fbdata.picture.data.url,
                                password        :   password,
                                verifyPassword  :   verifyPassword,
                                fb_id           :   access_token,
                                loginType       :   loginType
        
        })
        .then(function (response) {
            $scope.signInModal.hide();	
            console.log("logeed in");
        }, function (response) {
            $scope.loginMessage = response;
            console.log("not logeed in");
        });            
    }
        $state.go($state.current, {}, {reload: true}); 
        $scope.signInModal.hide();	
        $ionicLoading.hide();
}  

