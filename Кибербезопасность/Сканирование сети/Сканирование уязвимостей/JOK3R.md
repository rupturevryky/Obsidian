https://github.com/koutto/jok3r?tab=readme-ov-file
**To run:**
```
sudo docker run -i -t --name jok3r-container-updated -w /root/jok3r -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix --shm-size 2g --net=host koutto/jok3r
```
```
docker start -i jok3r-container-updated
```
**To open runing**:
```
docker exec -it <container id from docker ps> /bin/bash
```
**To update:** 
```
sudo docker pull koutto/jok3r
```
```
sudo docker run -i -t --name jok3r-container-updated -w /root/jok3r -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix --shm-size 2g --net=host koutto/jok3r
```

---

Справка: `python3 jok3r.py`
