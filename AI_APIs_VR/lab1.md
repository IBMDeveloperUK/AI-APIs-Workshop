# Workshop 1 - Trying out an Image Recognition service using Node-RED

In this workshop you will get your first hands-on with an Image Recogntion API using Node-RED.

This workshop is made using IBM Cloud for simplicity, though if you feel more comfortable with an other platform, an other Image Recognition service or if you want to run Node-RED on your machine, please do! 

## Before you begin

To complete this workshop you will need:
- A Node-Red instance
- A Visual Recognition service

**Creating a Node-RED app** (If you don't already have one yet)

1. [Sign up for an account here](https://ibm.biz/BdYMsy)
2. Verify your account by clicking on the link in the email sent to you
3. Log in to your IBM Cloud account
4. Click on "Catalog" on the top-right corner
5. Search and select "Node-RED Starter" 
6. Give a unique name to your app and click "Create"

**Provisioning the VR service**

1. Log in to your IBM Cloud account
2. Click on "Catalog" on the top-right corner
3. Search and select "Visual Recogition" 
4. Create the service

**Linking your Node-RED app and the VR service**

1. Go back to your dashboard by clicking on the IBM Cloud logo
2. Click on the name of the app you've previously created
3. Click on "Connections" on the left-hand side
4. Click on "Create Connection"
5. Click "Connect" next to your VR service and then "Restage"

### Building the flow
The flow will present a simple web page with a text field of where to input the image's URL, then submit it to Watson Visual Recognition. It will output the labels that have been found on the reply Web page.
![Reco-Lab-VisualRecognitionFlow.png](reco_lab_visual_recognition_flow.png)
The nodes required to build this flow are:

 - A ![`HTTPInput`](node_red_httpinput.png) node, configured with a `/reco` URL
 - A ![`switch`](node_red_switch.png) node which will test for the presence of the `imageurl` query parameter:
   ![Reco-Lab-Switch-Node-Props](reco_lab_switch_node_props.png)
 - A first ![template](node_red_template.png) node, configured to output an HTML input field and suggest a few selected images taken from the main Watson Visual Recognition demo web page:
```HTML
<html>
    <head>
        <title>Watson Visual Recognition on Node-RED</title>
    </head>
    <body>
    <h1>Welcome to the Watson Visual Recognition Demo on Node-RED</h1>
        <h2>Select an image URL</h2>
        <form  action="{{req._parsedUrl.pathname}}">
            <img src="https://raw.githubusercontent.com/watson-developer-cloud/visual-recognition-nodejs/master/public/images/samples/1.jpg" height='100'/>
            <img src="https://raw.githubusercontent.com/watson-developer-cloud/visual-recognition-nodejs/master/public/images/samples/2.jpg" height='100'/>
            <img src="https://raw.githubusercontent.com/watson-developer-cloud/visual-recognition-nodejs/master/public/images/samples/3.jpg" height='100'/>
            <img src="https://raw.githubusercontent.com/watson-developer-cloud/visual-recognition-nodejs/master/public/images/samples/4.jpg" height='100'/>
            <br/>Copy above image location URL or enter any image URL:<br/>
            <input type="text" name="imageurl"/>
            <input type="submit" value="Analyze"/>
        </form>
    </body>
</html>
```
![Reco-Lab-Template1-Node-Props](reco_lab_template1_node_props.png)

- A ![change](node_red_change.png) node (named `Extract img URL` here) to extract the `imageurl` query parameter from the web request and assign it to the payload to be provided as input to the Visual Recognition node:
![Reco-Lab-Change_and_Reco-Node-Props](reco_lab_change_and_reco_node_props.png)

 - The ![Watson Visual Recognition](node_red_watson_visual_recognition.png) node. Make sure that the credentials are setup from IBM Cloud, i.e. that the service is bound to the application. This can be verified by checking that the properties for the Visual Recognition node are clear:

 ![Visual Recognition node properties](reco_lab_visual_recognition_service_credentials.png)

 - And a final  ![`template`](node_red_template.png) node linked to the ![`HTTPResponse`](node_red_httpresponse.png) output node. The template will format the output returned from the Visual Recognition node into an HTML table for easier reading:
```HTML
<html>
    <head><title>Watson Visual Recognition on Node-RED</title></head>
    <body>
        <h1>Node-RED Watson Visual Recognition output</h1>
        <p>Analyzed image: {{payload}}<br/><img src="{{payload}}" height='100'/></p>
        <table border='1'>
            <thead><tr><th>Name</th><th>Score</th></tr></thead>
        {{#result.images.0.classifiers.0.classes}}
        <tr><td><b>{{class}}</b></td><td><i>{{score}}</i></td></tr>
        {{/result.images.0.classifiers.0.classes}}
        </table>
        <form  action="{{req._parsedUrl.pathname}}">
            <input type="submit" value="Try again"/>
        </form>
    </body>
</html>
```
![Reco-Lab-TemplateReport-Node-Props](reco_lab_templatereport_node_props.png)  
Note that the HTML snippet above has been simplified and stripped out of non-essential HTML tags, the completed flow solution has a complete HTML page.

### Testing the flow
To run the web page, point your browser to  `/http://xxxx.mybluemix.net/reco` and enter the URL of some  image.
The URL of the pre-selected images can be copied to clipboard and pasted into the text field.

The Watson Visual Recognition API will return an array with the recognized features, which will be formatted in a HTML table by the template:

![Visual RecognitionScreenshot ](reco_lab_visual_recognition_screenshot.png)

### Flow source
The complete flow is available [here](reco_lab_web_page.json).

## Visual Recognition Documentation
To find more information on the Watson Visual Recognition underlying service, visit these webpages :
- [Visual Recognition Documentation](https://console.bluemix.net/docs/services/visual-recognition/index.html#about)
- [Visual Recognition API Documentation](https://www.ibm.com/watson/developercloud/visual-recognition/api/v3/)

