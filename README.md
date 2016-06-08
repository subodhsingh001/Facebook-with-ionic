# Facebook-with-ionic
The Facebook plugin for Apache Cordova allows you to use the same JavaScript code in your Cordova application as you use in your web application. However, unlike in the browser, the Cordova application will use the native Facebook app to perform Single Sign On for the user. If this is not possible then the sign on will degrade gracefully using the standard dialog based authentication.
To work facebook you neded to install plugin first

Make sure you've registered your Facebook app with Facebook and have an APP_ID https://developers.facebook.com/apps.

Then install Cordova-plugin-facebook4 through cmd or gitbash anything use use

https://github.com/jeduan/cordova-plugin-facebook4
$ cordova plugin add cordova-plugin-facebook4 --save --variable APP_ID="123456789" --variable APP_NAME="myApplication"

Sometimes you will face problem to get logged in as token error ,but you can solve it in this way

This is how i solved this problem
Downloaded your APK to your PC in java jdk\bin folder
C:\Program Files\Java\jdk1.7.0_79\bin
go to java jdk\bin folder and run cmd then
copy the following command in your cmd
keytool -list -printcert -jarfile tris.apk
Copy the SHA1 value to your clip board
like this CD:A1:EA:A3:5C:5C:68:FB:FA:0A:6B:E5:5A:72:64:DD:26:8D:44:84
and open http://tomeko.net/online_tools/hex_to_base64.php10 to convert your SHA1 value to base64. This is what Facebook requires
get
zaHqo1xcaPv6CmvlWnJk3SaNRIQ= as our keyhash.


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

