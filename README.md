# AppRTCDemo
AppRTCDemo，base on njovy/AppRTCDemo

# 重要的修改记录
1. 匹配服务器ICE_SERVER_OVERRIDE不为none
RoomParametersFetcher.java 添加函数
```
private LinkedList<PeerConnection.IceServer> getIceServerFromPCConfigJSON(String pcConfig)
        throws JSONException {
        LinkedList<PeerConnection.IceServer> turnServers = new LinkedList<PeerConnection.IceServer>();
        JSONObject responseJSON = new JSONObject(pcConfig);
        JSONArray iceServers = responseJSON.getJSONArray("iceServers");
        for (int i = 0; i < iceServers.length(); ++i) {
            JSONObject server = iceServers.getJSONObject(i);
            JSONArray turnUrls = server.getJSONArray("urls");
            String username = server.has("username") ? server.getString("username") : "";
            String credential = server.has("credential") ? server.getString("credential") : "";
            for (int j = 0; j < turnUrls.length(); j++) {
                String turnUrl = turnUrls.getString(j);
                turnServers.add(new PeerConnection.IceServer(turnUrl, username, credential));
            }
        }
        return turnServers;
    }
```

将iceServersFromPCConfigJSON的调用替换为getIceServerFromPCConfigJSON
```
        // 原来模式
//      LinkedList<PeerConnection.IceServer> iceServers =
//          iceServersFromPCConfigJSON(roomJson.getString("pc_config"));
        // 庆富修改
        LinkedList<PeerConnection.IceServer> iceServers =
                getIceServerFromPCConfigJSON(roomJson.getString("pc_config"));
```
**注意：目前该处理没有智能识别ICE_SERVER_OVERRIDE的设置，在使用时需要注意，智能识别可以先研究web端的实现，该部分代码可以智能识别ICE_SERVER_OVERRIDE的情况，但AppRtcDemoe没有实现。**