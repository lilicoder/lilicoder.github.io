---
title: js 网页全屏
date: 2020-09-28 13:51:13
tags: js
---

实现网页全屏功能（F11），放在 react 中，其他的也一样
![large](large.png)

![small](small.png)

```
import fullscreen from "./icon/fullscreen.png";
import fullscreenexit from "./icon/fullscreenexit.png";

class IndexView extends Component {
  constructor(props) {
    super(props);
    this.state = {
      fullscreenEnabled: false,
      fullscreen: false
    };
  }
  troggleFullScreen() {
    var fullscreenEnabled =
      document.fullscreenEnabled ||
      document.mozFullScreenEnabled ||
      document.webkitFullscreenEnabled ||
      document.msFullscreenEnabled;
    if (!fullscreenEnabled) {
      return;
    }

    if (this.state.fullscreen) {
      //退出全屏
      if (document.exitFullscreen) {
        document.exitFullscreen();
      } else if (document.msExitFullscreen) {
        document.msExitFullscreen();
      } else if (document.mozCancelFullScreen) {
        document.mozCancelFullScreen();
      } else if (document.webkitExitFullscreen) {
        document.webkitExitFullscreen();
      }
    } else {
      var element = document.documentElement;
      if (element.requestFullscreen) {
        element.requestFullscreen();
      } else if (element.mozRequestFullScreen) {
        element.mozRequestFullScreen();
      } else if (element.msRequestFullscreen) {
        element.msRequestFullscreen();
      } else if (element.webkitRequestFullscreen) {
        element.webkitRequestFullScreen();
      }
    }
    this.setState({
      fullscreen: !this.state.fullscreen
    });
  }
  componentDidMount() {
    let _this = this;
    document.addEventListener("fullscreenchange", function() {
      var isFull = document.fullscreenElement;
      _this.setState({
        fullscreen: isFull
      });
    });
    document.addEventListener("keydown", e => {
      if (e.keyCode == 122) {
        e.preventDefault();
        this.troggleFullScreen();
      }
    });
  }
  render() {
    return (
      <div >
        <img
          src={this.state.fullscreen ? fullscreenexit : fullscreen}
          width="20"
          onClick={() => this.troggleFullScreen()}
          className="fullScreenIcon"
        />
      </div>
    );
  }
}


```
