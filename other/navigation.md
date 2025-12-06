# 站点导航

<div class="sites">
  <a class="site" href="">
    <img class="logo" src="" />
    <div class="name"></div>
    <div class="info"></div>
  </a>
</div>

<style>
  .sites {
    width: 100%;
    display: flex;
    flex-wrap: wrap;
  }
  .site {
    width: 220px;
    height: 220px;
    border-radius: 16px;
    margin: 20px;
    padding: 20px;
    border: 1px solid #dddddd;
    display: flex;
    flex-direction: column;
    text-decoration: none;
    transition: transform 0.3s, box-shadow 0.3s;
  }
  .site:hover {
    transform: translateY(-10px);
    box-shadow: 0px 15px 30px rgba(0, 0, 0, 0.1);
  }
  .logo {
    height: 80px;
    align-self: center;
  }
  .name {
    height: 40px;
    font-size: 24px;
    font-weight: 600;
    color: #34495e;
  }
  .info {
    height: 60px;
    font-size: 15px;
    font-weight: 400;
    color: #34495e;
    overflow: hidden;
    line-clamp: 3;
    text-overflow: ellipsis;
    display: -webkit-box;
    -webkit-box-orient: vertical;
    -webkit-line-clamp: 3;
  }
</style>
