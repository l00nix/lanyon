## Testing  the Cluster

{:start="9"}
9. Now that the limcluster was set up, I followed NetworkChuck's demos and first installed a couple of instances of nginx

{% include image.html
            img="/images/limcluster_build/nginx.png"
            title="limcluster_build16"
            caption="Fig 18: Nginx serving sample web page on the cluster" %}

{:start="10"}
10. After that, I tried the load balancing demo

{% include image.html
            img="/images/limcluster_build/rancher_cows.gif"
            title="limcluster_build17"
            caption="Fig 19: Rancher 'Hello World' load balancing demo running on the cluster" %}

{:start="11"}
11. I also did get the Minecraft server install working via Rancher GUI, exactly as described by NetworkChuck in his video.

{% include image.html
            img="/images/limcluster_build/limcluster-minecraft.png"
            title="limcluster_build18"
            caption="Fig 20: Minecraft deployment in the Rancher GUI" %}

{% include image.html
            img="/images/limcluster_build/limcluster-minecraft.gif"
            title="limcluster_build19"
            caption="Fig 21: Minecraft Java edition connecting to the minecraft server running on the limcluster" %}

**Note:** I did run into an issue that caused the minecraft deployment to 'crashloop' and constantly reboot. The issue is described [here:](https://stackoverflow.com/questions/58331710/launching-helm-chart-stable-minecraft-on-kubernetes-1-14-on-eks-fails-liveness-p){:target="_blank"}

I fixed it by changing the following values in the helm chart for the minecraft deployment:
```yaml
...
livenessProbe:
...
   initialDelaySeconds: 90
...
readinessProbe:
...
   initialDelaySeconds: 30
...
```

## Additional (Optional) - Adding an OLED display
{:start="12"}
12. The last step for me was to get the OLEDs working to display the stats of each node.

This was more of a fun add on that is not really necessary for the functionality of the cluster but I thought it would be nice to display basic system information on a small OLED display for easier identification of the indidual nodes.

See my seperate page on this side project for now: [oleddisplaystats](https://oleddisplaystats.loonix.ca/){:target="_blank"} and the more Raspberry Pi/limcluster specific: [displaypistats](https://displaypistats.loonix.ca/){:target="_blank"}

