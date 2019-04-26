package com.cnten.devicecache.task;

import com.cnten.devicecache.redis.impl.RedisHelperImpl;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Configuration;
import org.springframework.scheduling.annotation.EnableScheduling;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;
import org.springframework.util.StringUtils;

import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.Socket;

@Component
@Configuration      //1.主要用于标记配置类，兼备Component的效果。
@EnableScheduling   // 2.开启定时任务
public class RedisSchedulerTask {
    @Value("${cache.data-key}")
    private String dataKey;
    @Value("${cache.server-path}")
    private String serPath;
    @Value("${cache.server-port}")
    private int serPort;

    @Autowired
    private RedisHelperImpl redisHelperImpl;
    @Scheduled(cron = "* */1 * * * ?")
    private void process(){
        //redisHelperImpl.valuePut("device-cache","afasfasfdasf");
       String value = String.valueOf(redisHelperImpl.getValue(dataKey));
       if(!StringUtils.isEmpty(value)) {
           int startIndex = value.indexOf("$");
           int endIndex = value.indexOf("*");
           String message = value.substring(startIndex, endIndex + 3);
           String endMessage = value.substring(endIndex + 3, value.length());
           if(setMessage(message)){
               redisHelperImpl.valuePut(dataKey,endMessage);
           }
       }
    }


    private boolean setMessage(String message){
        boolean issuccess = false;
        Socket socket = null;
        OutputStream out = null;
        try {
            socket = new Socket(serPath, serPort);
            out = socket.getOutputStream();
            // 第一次请求服务器
            byte[] outputBytes1 = message.getBytes("UTF-8");
            out.write(outputBytes1);
            out.flush();
            System.out.println("==========================send server data:"+message+"==========================");
            issuccess = true;
        }catch (Exception e){
            e.printStackTrace();
            System.out.println("==========================send server data error==========================");
            issuccess = false;
        }finally {
            // 关闭连接
            if (out != null) {
                try {
                    out.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (socket != null) {
                try {
                    socket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
        return issuccess;
    }
}
