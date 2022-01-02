<!--
 * 严肃声明：
 * 开源版本请务必保留此注释头信息，若删除我方将保留所有法律责任追究！
 * 本系统已申请软件著作权，受国家版权局知识产权以及国家计算机软件著作权保护！
 * 可正常分享和学习源码，不得用于违法犯罪活动，违者必究！
 * Copyright (c) 2020 sherard all rights reserved.
 * 版权所有，侵权必究！
 *
-->

<template>
  <div class="container">
    <div class="home">
      <div class="header">
        <div class="title"><img src="../assets/logo.png" />cryptoSharing</div>
        <div class="tabs">
          <div
            class="item"
            v-bind:class="[{ active: tab == 'rent' }]"
            @click="switchTo('rent')"
          >
            RENT
          </div>
          <div
            class="item"
            v-bind:class="[{ active: tab == 'lend' }]"
            @click="switchTo('lend')"
          >
            LEND
          </div>
          <div
            class="item"
            v-bind:class="[{ active: tab == 'dash' }]"
            @click="switchTo('dash')"
          >
            MY DASHBOARD
          </div>
        </div>
        <div class="select" v-if="isLogin">
          <div class="ntf-search" @click="searchDicshow"> 
            <span>{{nowNtfName }}</span>
            <img src="../assets/xiala.png" height="10" width="10">
          </div>
          <div class="ntf-search" style="width: 170px;" @click="searchDicshow2"> 
            <span>{{nowNtfName2 }}</span>
            <img src="../assets/xiala.png" height="10" width="10">
          </div>
          

        </div>
        <div class="contact">
          <a-button v-if="!isLogin" class="login" type="primary" @click="goTo"
            >Connect wallet</a-button
          >
          <!-- <img v-if="isLogin" src="../assets/person.svg"/> -->
          <img v-if="isLogin" src="../assets/wallet.svg" @click="showDrawer" />
        </div>
      </div>
      <img class="collections" src="./../assets/view_bg.png" />
      <div class="content" v-if="tab == 'rent'">
        <div class="nologin" v-if="!isLogin">
          You don't have any NFTs to rent
        </div>
        <div v-if="isLogin" class="detail_container">
          <div class="title">Explore Rent Collections</div>
          <div class="detail_bag" v-if="rentDetails && !_rentLoading">
            <div class="detail_total">{{ rentDetails.total }} items</div>
            <div class="main-content">
              <div
                v-for="(item, index) in rentDetails"
                :key="index"
                class="min-item"
                v-show="!item.notshow"
              >
                <img :src="lendImg[index]" />
                <div class="name">{{ item.id }}</div>
                <div class="tags">
                  <div class="tag flex">price：<div>{{item.price}} USDT/s</div></div>
                  <div class="tag">maxRentTime：<div>{{item.maxRentTime}}</div></div>
                  <div class="tag flex">rentLock：<div>{{item.rentLock}}</div></div>
                  <div class="tag">rentTime：<div>{{item.rentTime}}</div></div>
                </div>
                <div class="desc">
                  <a-button class="normal-btn" type="primary" @click="rentItem(item.id, index)"
                    >Rent</a-button
                  >
                </div>
              </div>
            </div>
          </div>
          <div style="text-align: center;padding: 40px 0;" v-if="_rentLoading">
            <a-spin />
          </div>
        </div>
      </div>
      <div class="content" v-if="tab == 'lend'">
        <div class="nologin" v-if="!isLogin">
          You don't have any NFTs to lend
        </div>
        <div v-if="isLogin" class="detail_container">
          <div class="title">Explore Lend Collections</div>
          <div class="detail_bag" v-if="lendDetails && !_lendLoading">
            <div class="detail_total">{{ lendDetails.length }} items</div>
            <div class="main-content">
              <div
                v-for="(id, index) in lendDetails"
                :key="index"
                class="min-item"
              >
                <img :src="lendImg[index]" />
                <div class="name">{{ id }}</div>
                <div class="desc">
                  <a-button class="normal-btn" type="primary" @click="lendItem(id, index)"
                    >Lend</a-button
                  >
                </div>
              </div>
            </div>
          </div>
          <div style="text-align: center;padding: 40px 0;" v-if="_lendLoading">
            <a-spin />
          </div>
        </div>
      </div>
      <div class="content" v-if="tab == 'dash'">
        <div class="nologin" v-if="!isLogin">
          You don't have any NFTs in your dashboard
        </div>
        <div v-if="isLogin" class="detail_container">
          <div class="title">Explore dashboard Collections</div>
          <div class=""></div>
          <div class="detail_bag" v-if="dashDetails && !_dashLoading">
            <div class="detail_total">{{ dashDetails.length }} items</div>
            <div class="main-content">
              <div
                v-for="(id, index) in dashDetails"
                :key="index"
                class="min-item"
              >
                <img :src="lendImg[index]" />
                <div class="name">{{ id }}</div>
                <div class="desc">
                  <a-button class="normal-btn" type="primary" @click="settingItem(id, index)"
                    >setting</a-button
                  >
                </div>
              </div>
            </div>
          </div>
          <div style="text-align: center;padding: 40px 0;" v-if="_dashLoading">
            <a-spin />
          </div>
        </div>
      </div>
    </div>

    <!-- search 弹窗  -->
    <div class="mask" id="searchMask" v-if="_searchDicshow" @click="closeSearch">
      <div class="search-form">
        <div class="title">选择代币</div>
        <a-input
          class="input"
          v-model:value="searchNtf"
          placeholder="搜索名称或粘贴地址"
          @change="searchOnChange"
          />
        <div class="common-tips">
          常用代币
          <img src="../assets/yips.png" height="20" width="20">
        </div>
        <div class="common">
          <div class="item">
            <img src="../assets/toast_1.png">
            <span>AXS</span>
          </div>
          <div class="item">
            <img src="../assets/toast_2.png">
            <span>MANA</span>
          </div>
          <div class="item">
            <img src="../assets/toast_3.png">
            <span>SAND</span>
          </div>
        </div>

        <div class="all-ntf">
          <div v-for="(ntf, index) in ntfsFilterItems" :key="index" class="item"  @click="confirmSearch(ntf, index)">
            <img :src="lendGameImg[index]" >
            <div class="bag">
              <div class="name">{{ntf._name}}</div>
              <div class="symbol">{{ntf._symbol}}</div>
            </div>
          </div>
        </div>
      </div>
      <div class="x"  @click="_searchDicshow = false">X</div>
    </div>
    <!-- search2 弹窗  -->
    <div class="mask" id="searchMask2" v-if="_searchDicshow2" @click="closeSearch2">
      <div class="search-form">
        <div class="title">选择代币</div>
        <a-input
          class="input"
          v-model:value="searchNtf"
          placeholder="搜索名称或粘贴地址"
          @change="searchOnChange"
          />
        <div class="common-tips">
          常用代币
          <img src="../assets/yips.png" height="20" width="20">
        </div>
        <div class="common">
          <div class="item">
            <img src="../assets/eth.png">
            <span>ETH</span>
          </div>
          <div class="item">
            <img src="../assets/usdc.png">
            <span>USDC</span>
          </div>
          <div class="item">
            <img src="../assets/usdt.png">
            <span>USDT</span>
          </div>
        </div>

        <div class="all-ntf">
          <div v-for="(ntf, index) in ntfsFilterItems2" :key="index" class="item">
            <img :src="biteBi[index]" >
            <div class="bag">
              <div class="name">{{ntf.name}}</div>
              <div class="symbol">{{ntf.desc}}</div>
            </div>
          </div>
        </div>
      </div>
      <div class="x"  @click="_searchDicshow2 = false">X</div>
    </div>
    <!-- 侧边栏  -->
    <div>
      <a-drawer
        class="drawer"
        placement="right"
        :closable="false"
        :visible="drawerVisible"
        :after-visible-change="afterVisibleChange"
        @close="onClose"
        width="400"
      >
        <div class="content" v-if="isLogin">
          <div class="title">
            <div class="online">
              <img src="../assets/online.png" />
            </div>
            <a-collapse
              style="width: 130px;"
              class="expand"
              v-model="activeKey"
              :expand-icon-position="expandIconPosition"
            >
              <a-collapse-panel key="1" header="My Wallet">
                <div
                  style="display:flex;align-items: center;cursor: pointer;"
                  @click="logOut"
                >
                  <LogoutOutlined style="margin-right:14px;" />
                  <p style="margin-bottom: 0;">Log out</p>
                </div>
              </a-collapse-panel>
            </a-collapse>
            <a-tooltip class="account">
              <template #title>{{ user.accountId }}</template>
              {{ user.accountId }}
            </a-tooltip>
          </div>
          <div class="money">
            <div class="balance">Total balance</div>
            <div class="btn">add Funds</div>
          </div>
        </div>
      </a-drawer>
    </div>
    <!-- rent -->
    <a-modal
      :visible="modalRentVisible"
      title="fill rent information"
      style="top: 20px"
      :footer="false"
      :closable="false"
      width="400px"
      :confirmLoading="confirmRentLoading"
      @cancel="modalRentVisible = false"
    >
      <a-form :model="rentFormState">
        <a-form-item label="How many day you want rent" name="rentDay">
          <a-input
            v-model:value="rentFormState.rentDay"
            type="number"
            placeholder="fill nft price"
          />
        </a-form-item>
      </a-form>
      <div class="footer">
        <a-button
          class="normal-btn"
          :disabled="approveRentDisabled"
          type="primary"
          :loading="approveRentLoading"
          @click="approveRent(id, index)"
          >Approve</a-button
        >
        <a-button
          class="normal-btn"
          :disabled="confirmRentDisabled"
          type="primary"
          :loading="confirmRentLoading"
          @click="confirmRent(id, index)"
          >Confirm</a-button
        >
      </div>
    </a-modal>

    <!-- lend  -->
    <a-modal
      :visible="modalVisible"
      title="fill lend information"
      style="top: 20px"
      :closable="false"
      width="400px"
      :confirmLoading="confirmLendLoading"
      @cancel="modalVisible = false"
      :footer="false"
    >
      <a-form :model="lendFormState">
        <a-form-item label="lend deadline" name="lendTime">
          <a-date-picker
            v-model:value="lendFormState.lendTime"
            show-time
            type="date"
            allowClear="false"
            placeholder="Pick a date"
            style="width: 100%"
            :disabledMinutes="true"
          />
        </a-form-item>
        <a-form-item label="lend how many nft a day" name="lendMoney">
          <a-input
            v-model:value="lendFormState.lendMoney"
            placeholder="fill nft price"
          />
        </a-form-item>
      </a-form>
      <div class="footer">
        <a-button
          class="normal-btn"
          :disabled="approveLendDisabled"
          type="primary"
          :loading="approveLendLoading"
          @click="approveLend(id, index)"
          >Approve</a-button
        >
        <a-button
          class="normal-btn"
          :disabled="confirmLendDisabled"
          type="primary"
          :loading="confirmLendLoading"
          @click="confirmLend(id, index)"
          >Confirm</a-button
        >
      </div>
    </a-modal>
  </div>
</template>

<script>
import { reactive, toRefs, onMounted } from 'vue';
import detectEthereumProvider from '@metamask/detect-provider';
import { message } from 'ant-design-vue';
import { getUser, setUser, resetUser } from '../store/user';
import { ethers } from 'ethers';
import { LogoutOutlined } from '@ant-design/icons-vue';
import { cryptoSharing } from './abi/cryptoSharing';
import { formatDate } from './abi/util';
import { ERC9999EnumerableAbi } from './abi/ERC9999EnumerableAbi';
import { ERC20Abi } from './abi/ERC20Abi';
export default {
  components: {
    LogoutOutlined
  },
  setup() {
    const state = reactive({
      tab: 'rent',
      isLogin: false,
      user: null,
      sort: '1',
      ntfsFilterItems:[],
      ntfsFilterItems2:[
        {name:'ETH',desc:'ETH'},
        {name:'USDT',desc:'USDT'}
      ],
      ntfsItems:[],
      searchNtf:'',
      nowNtfName:'',
      Factory: '0xDa4696D9503C20A2aE212254c27276646744f3d2',
      nowNtfName2:'SELECT PAY TOKEN',
      _searchDicshow:false,
      _searchDicshow2:false,
      drawerVisible: false,
      expandIconPosition: 'right',
      provider: null,
      modalVisible: false,
      _lendLoading: false,
      _rentLoading: false,
      _dashLoading:false,
      lendFormState: {
        lendTime: null,
        lendMoney: null
      },
      modalRentVisible: false,
      rentFormState: {
        rentDay: null
      },
      biteBi:[
        require('./../assets/eth.png'),
        require('./../assets/usdc.png'),
        require('./../assets/usdt.png'),
      ],
      sortArr: [
        { value: '1', label: 'PRICE: HIGH TO LOW' },
        { value: '2', label: 'PRICE: LOW TO HIGH' }
      ],
      poolAddress: [
        '0x6Af04c4dd5BC159fDD3C8e3Ef8E5C1766A185a4c', //AXS USDT
        '0xcB3004c42187c124D6903eD002a949Fc5266A34D', //SAND USDT
        '0xB563dD0A6790DFBaDbD687988E8628EA32EE44D4'  //MANA USDT
      ],
      rentImg: [
        '../assets/icon1.jpeg',
        '../assets/icon2.jpeg',
        '../assets/icon3.jpeg'
      ],
      rentList: [{ id: 1 }, { id: 2 }, { id: 3 }],
      /* lend 信息  */

      rentGameList: null,
      NFTAddress: [
        '0x9a8E2c523D76151b4514281FeCe5E28439B3cCFe', //AXS
        '0x344AC4347cAFAE359962188fBe4aE16f738Cca46', //SAND
        '0xa0C86f099676e6642CFcB1e52fC95cC6C0E0AC3e' //MANA
      ],
      lendGameList: null,
      lendGameImg: [
        require('./../assets/lend_game.jpeg'),
        require('./../assets/lend_game2.jpeg'),
        require('./../assets/lend_game3.jpeg')
      ],
      lendImg: [
        require('../assets/lend_icon_1.jpeg'),
        require('../assets/lend_icon_2.jpeg'),
        require('../assets/lend_icon_3.jpeg'),
        require('../assets/lend_icon_4.jpeg'),
        require('../assets/lend_icon_5.jpeg'),
        require('../assets/lend_icon_6.jpeg'),
        require('../assets/lend_icon_7.jpeg'),
        require('../assets/lend_icon_8.jpeg'),
        require('../assets/lend_icon_9.jpeg'),
        require('../assets/lend_icon_10.jpeg')
      ],
      lendDetails: null,
      rentDetails: null,
      rentAddressIndex: null,
      lendAddressIndex: null,
      confirmLendLoading: false,
      approveLendDisabled: false,
      approveLendLoading: false,
      confirmLendDisabled: false,
      lendInfo: {},
      axsNFTContractMap:{},
      confirmRentLoading: false,
      approveRentDisabled: false,
      approveRentLoading: false,
      confirmRentDisabled: false,
      rentInfo: {},
      USDTContractMap:{},

      dashGameList:[],
      dashDetails: null,
      gamePoolArr:[],
      gameNFTArr:[],
    });

    onMounted(() => {
      state.provider = new ethers.providers.Web3Provider(window.ethereum);
      authUser();
      init();
      onNetworkChange();
      window.ethereum.on('accountsChanged', handleAccountsChanged);
    });
    const onNetworkChange = ()=> {

    };
    const handleAccountsChanged = (accounts) => {
      console.log(accounts);
      if(accounts && accounts.length>0) {
        message.info(`Your account changed to ${accounts[0]}`);
        const accountId = accounts[0];
        state.user.accountId = accountId;
        init();
      }
    }

    //const router = useRouter();
    // get started
    const goTo = async () => {
      const provider = await detectEthereumProvider();
      console.log(provider);
      if (provider) {
        const accounts = await window.ethereum.request({
          method: 'eth_requestAccounts'
        });
        const accountId = accounts[0];
        console.log(accountId);
        setUser({ accountId, authTime: new Date().getTime(), isLogin: true });
        authUser();
      } else {
        message.warning('You should install MetaMask first! ');
      }
    };

    const authUser = async () => {
      state.user = getUser();
      console.log('-----authUser--------');
      console.log(state.user);
      if (!state.user.accountId) {
        state.user.isLogin = false;
        state.isLogin = false;
      } else {
        setUser({ authTime: new Date().getTime(), isLogin: true });
        state.isLogin = true;
      }
    };
    const switchTo = t => {
      state.tab = t;
      console.log(t);
      init();
    };

    const init = () => {
      if (state.tab == 'lend') {
        lendInit();
      } else if (state.tab == 'rent') {
        rentInit();
      } else if (state.tab == 'dash') {
        dashInit();
      }
    };

    const searchOnChange = ()=>{
      const arr = state.ntfsItems.filter(item=>{
        return item._name.indexOf(state.searchNtf) >-1;
      })
      state.ntfsFilterItems = arr;
    }
    const closeSearch = (event) => {
       if (event.target == document.getElementById('searchMask')) {
        state._searchDicshow = false;
      }
    }
    const closeSearch2 = (event) => {
       if (event.target == document.getElementById('searchMask2')) {
        state._searchDicshow2 = false;
      }
    }

    const searchDicshow = () => {
      state._searchDicshow = true;
      state.searchNtf = '';
      state.ntfsFilterItems = state.ntfsItems;
    }

    const searchDicshow2 = () => {
      state._searchDicshow2 = true;
      state.searchNtf2 = '';
    }

    const confirmSearch = (ntf)=>{
      state.nowNtfName = ntf._name;
      if(state.tab=='lend') {
        showLendGameDeail(ntf, 0);
      }else if(state.tab =='rent') {
        showRentGameDeail(ntf._poolAddress, 0);
      }else if(state.tab =='dash') {
        //showRentGameDeail(ntf._address, 0);
      }
      state._searchDicshow = false;
    }

    const handleNtfSelectChange = (value, option) =>{
      console.log('handleNtfSelectChange');
      if(state.tab == 'lend') {
        showLendGameDeail(value._address, option.key);
      }else if(state.tab == 'rent') {
        showRentGameDeail(value._poolAddress, option.key);
      }else if(state.tab == 'dash') {
        //showRentGameDeail(value._address, option.key);
      }
    };

    const handleSortChange = (value, option) => {
      console.log(value);
      console.log(option);
      console.log(state.sort);
    };
    const showDrawer = () => {
      state.drawerVisible = true;
    };
    const onClose = () => {
      state.drawerVisible = false;
    };

    const rentInit = async () => {
      state.rentDetails = null;
      state._rentLoading = true;
      state.rentGameList = null;
      const gameArr = state.poolAddress.map(addr => {
        const provider = new ethers.providers.Web3Provider(window.ethereum);
        const providerSign = provider.getSigner();
        return new ethers.Contract(addr, cryptoSharing, providerSign); // 
      });
      const gameResults = await Promise.all(gameArr);
      for (let i = 0; i < gameResults.length; i++) {
        gameResults[i]._poolAddress = state.poolAddress[i];
        //gameResults[i]._name = await gameResults[i].name();
        //gameResults[i]._symbol = await gameResults[i].symbol();
      }
      state.rentGameList = gameResults;
      state._rentLoading = false;
      state.ntfsItems = gameResults;  //下拉框为所有ntfs
      state.gamePoolArr = gameResults;   // 保存合约结果
      state.ntfsFilterItems = gameResults;
      state.nowNtfName = gameResults[0]._name;
      showRentGameDeail(gameResults[0]._poolAddress, 0);
      lendInit(true);
    };
    const showRentGameDeail = async (_poolAddress, index) => {
      state.rentAddressIndex = index;
      state._rentLoading = true;
      state.rentDetails = null;
      state.lendInfo = {};
      const provider = new ethers.providers.Web3Provider(window.ethereum);
      console.log(_poolAddress);
      const providerSign = provider.getSigner();
      const axsContract = new ethers.Contract(_poolAddress, cryptoSharing, providerSign);
      const total = (await axsContract.totalSupply()).toNumber();
      console.log(total);
      const pArr = [];
      for (let i = 0; i < total; i++) {
        pArr.push(axsContract.tokenByIndex(i));
      }
      console.log(pArr);
      const idResults = await Promise.all(pArr);
      console.log(idResults);
      const ids = idResults.map(item => {return {id:item.toNumber()}});
      console.log(ids);
      fetchRentItemInfo(ids);
    };
    const fetchRentItemInfo = async (ids)=>{
       const provider = new ethers.providers.Web3Provider(window.ethereum);
       const providerSign = provider.getSigner();
       const axsContract = new ethers.Contract(
        state.poolAddress[state.rentAddressIndex],
        cryptoSharing,
        providerSign
      );
      let total = 0;
      for(let i =0; i<ids.length;i++) {
        ids[i].price = (await axsContract.getPrice(ids[i].id)).toNumber()
        ids[i]._maxRentTime = (await axsContract.getMaxRentTime(ids[i].id)).toNumber()
        if(Number(ids[i]._maxRentTime+'000') < new Date().getTime()) ids[i].notshow = true; //_maxRentTime 最大可租时间小于当前时间，不显示
        ids[i].maxRentTime = formatDate(new Date(Number(ids[i]._maxRentTime+'000')), 'yyyy-MM-dd hh:mm');
        ids[i].rentLock = (await axsContract.getRentLock(ids[i].id))
        ids[i]._rentTime = (await axsContract.getRentTime(ids[i].id)).toNumber()
        if(Number(ids[i]._rentTime+'000') > new Date().getTime()) ids[i].notshow = true; //_rentTime 别人租到此item的到期时间大于当前时间，不显示
        ids[i].rentTime = formatDate(new Date(Number(ids[i]._rentTime+'000')), 'yyyy-MM-dd hh:mm');
        if(!ids[i].notshow) total+=1;
      }
      state.rentDetails = ids;
      state.rentDetails.total = total;
      state._rentLoading = false;
       
    };
    // now
    const lendInit = async (flag) => {
      if(flag) {
        const gameArr = state.NFTAddress.map(addr => {
          const provider = new ethers.providers.Web3Provider(window.ethereum);
          return new ethers.Contract(addr, ERC9999EnumerableAbi, provider);
        });
        const gameResults = await Promise.all(gameArr);

        for (let i = 0; i < gameResults.length; i++) {
          gameResults[i]._address = state.NFTAddress[i];
          gameResults[i]._name = await gameResults[i].name();
          gameResults[i]._symbol = await gameResults[i].symbol();
          //gameResults[i]._totalSupply =  (await gameResults[i].totalSupply()).toNumber();
        }
        state.nowNtfName = gameResults[0]._name;
        if(state.gamePoolArr) {
          state.gamePoolArr.forEach((item,index)=>{
            state.gamePoolArr[index]._name = gameResults[index]._name;
            state.gamePoolArr[index]._symbol = gameResults[index]._symbol;
          })
        }
        state.nowNtfName = gameResults[0]._name;
      }else{
        state.lendDetails = null;
        state._lendLoading = true;
        state.lendGameList = null;
        const gameArr = state.NFTAddress.map(addr => {
          const provider = new ethers.providers.Web3Provider(window.ethereum);
          return new ethers.Contract(addr, ERC9999EnumerableAbi, provider);
        });
        const gameResults = await Promise.all(gameArr);

        for (let i = 0; i < gameResults.length; i++) {
          gameResults[i]._address = state.NFTAddress[i];
          gameResults[i]._name = await gameResults[i].name();
          gameResults[i]._symbol = await gameResults[i].symbol();
          //gameResults[i]._totalSupply =  (await gameResults[i].totalSupply()).toNumber();
        }
        state.lendGameList = gameResults;
        state._lendLoading = false;
        state.ntfsItems = gameResults;  //下拉框为所有ntfs
        state.gameNFTArr = gameResults;  // 保存合约结果
        state.ntfsFilterItems = gameResults;
        state.nowNtfName = gameResults[0]._name;
        showLendGameDeail(gameResults[0], 0);
        console.log(gameResults);
      }
      
    };
    const showLendGameDeail = async (axsContract, index) => {
      console.log(axsContract);
      console.log(index);
      state._lendLoading = true;
      state.lendDetails = null;
      state.lendAddressIndex = index;
      state.lendInfo = {};
      console.log(axsContract);
      console.log(state.user.accountId);
      await updateAccount();
      const totalRes = await axsContract.balanceOf(state.user.accountId); // 游戏里有几个物品
      const total = totalRes.toNumber();
      console.log(total);
      const pArr = [];
      for (let i = 0; i < total; i++) {
        pArr.push(axsContract.tokenOfOwnerByIndex(state.user.accountId, i));
      }
      console.log(pArr);
      const idResults = await Promise.all(pArr);
      console.log(idResults);
      const ids = idResults.map(item => item.toNumber());
      console.log(ids); //  nft 的 id */
      state.lendDetails = ids;
      state._lendLoading = false;
    };
    const lendItem = (tokenId) => {
      console.log(tokenId); // 10002
      state.tokenId = tokenId;
      state.lendFormState.lendTime = null;
      state.lendFormState.lendMoney = 0;
      state.modalVisible = true;
      state.confirmLendDisabled = true;
      state.approveLendDisabled = false;
      state.approveLendLoading = false;
      state.confirmLendLoading = false;
    };
    const approveLend = async () => {
      const now = new Date().getTime();
      const lendTime = state.lendFormState.lendTime.valueOf();
      if (lendTime <= now)
        return message.error('lend date must be greater than now!');
      if (!state.lendFormState.lendMoney)
        return message.error('price require!');
      const lendMoney = Math.round(state.lendFormState.lendMoney / 86400) + 1;
      const provider = new ethers.providers.Web3Provider(window.ethereum);
      const providerSign = provider.getSigner();

      const axsAddress = state.poolAddress[state.lendAddressIndex]; // "0x20366D9EEDFFFA1aF4f7Cf0394D77c772258a8D0";
      const axsNFTAddress = state.NFTAddress[state.lendAddressIndex]; //  "0x2f359E67aFe0b0A11D7715bDDe889211C518828f"

      const axsContract = new ethers.Contract(axsAddress, cryptoSharing, providerSign);
      const axsNFTContract = new ethers.Contract(
          axsNFTAddress,
          ERC9999EnumerableAbi,
          providerSign
      );
      if(state.axsNFTContractMap[axsNFTAddress]) {
        console.log(axsNFTAddress+' axsNFTContract 已存在');
      }else{
        const filterFrom = axsNFTContract.filters.Approval(providerSign.address);
        axsNFTContract.on(filterFrom, (from/* from, to, amount, event */) => {
          console.log(from);
          state.approveLendLoading = false;
          state.approveLendDisabled = true;
          state.confirmLendDisabled = false;
          message.success('Approve success, please confirm lend!');
          state.lendInfo[state.tokenId].tokenId = state.tokenId;
          state.lendInfo[state.tokenId].lendTime = Math.round(lendTime / 1000);
          state.lendInfo[state.tokenId].lendMoney = lendMoney;
        });
        state.lendInfo[state.tokenId] ={axsContract:axsContract,axsNFTContract:axsNFTContract};
        state.axsNFTContractMap[axsNFTAddress] = axsNFTContract;
      }
      state.approveLendLoading = true;
      axsNFTContract
        .approve(axsAddress, state.tokenId)
        .then(result => {
          console.log(result);
        })
        .catch(err => {
          console.error(err);
          state.approveLendLoading = false;
          state.modalVisible = false;
          message.error('Approve error!');
        });
    };

    const confirmLend = () => {
      state.confirmLendLoading = true;
      state.lendInfo[state.tokenId].axsContract
        .lendNFT(
          state.lendInfo[state.tokenId].tokenId,
          state.lendInfo[state.tokenId].lendTime,
          state.lendInfo[state.tokenId].lendMoney
        )
        .then(res => {
          console.log(res);
          state.confirmLendLoading = false;
          state.modalVisible = false;
          message.success('lend success!');
        })
        .catch(err => {
          state.modalVisible = false;
          console.error(err);
          message.error('lend error!');
        });
    };

    const rentItem = tokenId => {
      state.tokenId = tokenId;
      state.rentFormState.rentDay = 1;
      state.modalRentVisible = true;
      state.confirmRentDisabled = true;
      state.approveRentDisabled = false;
      state.approveRentLoading = false;
      state.confirmRentLoading = false;
    };

    const approveRent = async () =>{
      if (!state.rentFormState.rentDay || state.rentFormState.rentDay <= 0)
        return message.error('rent day error!');
      const rentDay = state.rentFormState.rentDay;
      const USDT = '0x6aa76A1fA0Afc3B96a026A8f5c21f7D785163C67';
      const provider = new ethers.providers.Web3Provider(window.ethereum);
      const providerSign = provider.getSigner();
      const USDTContract = new ethers.Contract(USDT, ERC20Abi, providerSign);
      const axsContract = new ethers.Contract(
        state.poolAddress[state.rentAddressIndex],
        cryptoSharing,
        providerSign
      );

      if(state.USDTContractMap[USDT]) {
        console.log(USDT+' USDTContract 已存在');
      }else{
        const filterFrom = USDTContract.filters.Approval(providerSign.address);
        USDTContract.on(filterFrom, (from/* from, to, amount, event */) => {
          console.log(from);
          state.approveRentLoading = false;
          state.approveRentDisabled = true;
          state.confirmRentDisabled = false;
          message.success('Approve success, please confirm rent!');
          state.rentInfo[state.tokenId].nowtime = Math.floor(new Date().getTime() / 1000) - 60;
          state.rentInfo[state.tokenId].rentDay = rentDay;
          state.USDTContractMap[USDT] = USDTContract;

        });
        state.rentInfo[state.tokenId] ={axsContract:axsContract,USDTContract:USDTContract};
      }

      const _price = (await axsContract.getPrice(state.tokenId)).toNumber();
      const price = _price * rentDay * 3600 * 24;
      state.approveRentLoading = true;
      console.log(state.poolAddress[state.rentAddressIndex])
      console.log(price)
      USDTContract.approve(state.poolAddress[state.rentAddressIndex], price)
        .then(result => {
          console.log(result);
        })
        .catch(err => {
          state.approveLendLoading = false;
          state.modalRentVisible = false;
          console.error(err);
          message.error('approve error!');
        });

    };
    const confirmRent = async () =>{
      const time = state.rentInfo[state.tokenId].nowtime + state.rentInfo[state.tokenId].rentDay * 3600 * 24;
      console.log(state.tokenId)
      console.log(time)
      state.confirmRentLoading = true;
      state.rentInfo[state.tokenId].axsContract
              .rentNFT(state.tokenId, time)
              .then(res => {
                console.log(res);
                state.confirmRentLoading = false;
                message.success('rent success!');
                state.modalRentVisible = false;
              })
              .catch(err => {
                state.confirmRentLoading = false;
                state.modalRentVisible = false;
                console.error(err);
                message.error('rent error!');
              });
    };


    const logOut = () => {
      resetUser();
      state.user = null;
      state.isLogin = false;
      state.drawerVisible = false;
    };

    const updateAccount = async () => {
      const accounts = await window.ethereum.request({
        method: 'eth_requestAccounts'
      });
      const accountId = accounts[0];
      console.log(accountId);
      state.user.accountId = accountId;
    };

    const dashInit = async ()=>{
      state.dashDetails = null;
      state._dashLoading = true;
      state.dashGameList = null;

      //

      state.dashGameList = [];
      state._dashLoading = false;
      state.ntfsItems = [];  //下拉框为所有ntfs
      state.ntfsFilterItems = [];
      state.nowNtfName = '';
      showDashboardDeail('', 0);
    }

    const showDashboardDeail = async ()=> {
      //state.dashDetails = [1000,1001];
    }

    return {
      ...toRefs(state),
      goTo,
      switchTo,
      handleSortChange,
      searchDicshow,
      searchDicshow2,
      handleNtfSelectChange,
      showDrawer,
      onClose,
      showLendGameDeail,
      lendItem,
      confirmLend,
      showRentGameDeail,
      rentItem,
      confirmRent,
      logOut,
      approveLend,
      approveRent,
      confirmSearch,
      searchOnChange,
      closeSearch,
      closeSearch2,
      dashInit
    };
  }
};
</script>

<style lang="less">
.container {
  .home {
    min-height: 100vh;
    background-image: rgb(255, 255, 255);
    box-sizing: border-box;
    display: flex;
    flex-direction: column;

    .header {
      box-shadow: rgb(4 17 29 / 25%) 0px 0px 8px 0px;
      max-width: 100vw;
      height: 72px;
      position: sticky;
      top: 0px;
      z-index: 110;
      transition: top 0.5s ease 0s;
      background-color: rgb(255, 255, 255);
      display: flex;
      justify-content: space-between;
      align-items: center;
      box-shadow: rgba(4, 17, 29, 25%) 0px 0px 8px 0px;

      .title {
        font-size: 24px;
        font-weight: 600;
        display: flex;
        align-items: center;
        font-weight: 600;
        font-size: 30px;
        letter-spacing: 0px;
        color: rgb(4, 17, 29);

        img {
          height: 40px;
          margin-right: 6px;
          margin-left: 20px;
        }
      }

      .tabs {
        display: flex;
        flex: 1;
        margin-left: 4vw;

        .item {
          margin: 0 30px;
          transition: color 0.4s ease 0s, background-color 0.4s ease 0s;
          color: #707a83;
          font-size: 14px;
          font-weight: 600;
          position: relative;
          cursor: pointer;

          &.active {
            color: black;
            border-bottom: 2px solid #000000;
          }
        }
      }

      .select {
        flex: 0;
        margin-right: 20px;
        display: flex;

        .ntf-search {
          border: 1px solid #d9d9d9;
          width: 140px;
          margin-right: 10px;
          display: flex;
          align-items: center;
          color: rgba(0, 0, 0, 0.85);
          justify-content: space-between;
          padding: 0 10px;
          cursor: pointer;

          > span {
            flex:1;
          }

          > img {
            flex: 0 0 10px;
          }
        }
      }

      .contact {
        display: flex;
        font-size: 24px;

        > span,
        button,
        img {
          margin-right: 30px;
          cursor: pointer;
        }

        > img {
          height: 24px;
        }
      }
    }

    .collections {
      width: 100vw;
    }

    .content {
      flex: 0 0 35%;
      display: flex;
      flex-direction: column;
      justify-content: space-around;
      background: #F5F5F5;

      .detail_container {
        width: 90%;
        margin: 0 auto;
        background: #ffffff;
        border-radius: 45px;
        margin-top: -171px;
        min-height: calc(100vh - 250px);
      }

      .nologin {
        text-align: center;
        font-weight: 600;
        font-size: 30px;
        letter-spacing: 0px;
        color: rgb(115 125 134);
        margin-top: 10%;
      }

      .title {
        margin-top: 40px;
        text-align: center;
        font-weight: 600;
        font-size: 22px;
        letter-spacing: 0px;
        color: rgb(4, 17, 29);
      }

      .sort {
        justify-content: end;
        display: flex;
        padding: 0 40px;
        margin-bottom: 20px;
      }

      .detail_total {
        text-align: left;
        padding-left: 4vw;
        font-size: 20px;
        color: #3f5e7a;
        margin-bottom: 10px;
      }

      .main-content {
        display: flex;
        flex-wrap: wrap;
        padding: 0 20px;
        justify-content: space-around;

        @media screen and (max-width: 1800px) {
          .item {
            flex: 0 0 30vw !important;
            height: 30vw !important;
          }
        }

        @media screen and (max-width: 1100px) {
          .item {
            flex: 0 0 46vw !important;
            height: 46vw !important;
          }
        }

        @media screen and (max-width: 800px) {
          .item {
            flex: 0 0 80vw !important;
            height: 80vw !important;
          }
        }

        .item {
          flex: 0 0 45vw;
          height: 45vw;
          border: 1px solid #e5e8eb;
          background-color: rgb(255, 255, 255);
          border-radius: 10px;
          cursor: pointer;
          margin-bottom: 20px;

          > img {
            width: 100%;
            height: 80%;
            object-fit: cover;
            border-radius: 10px;
          }

          .name {
            color: rgb(4, 17, 29);
            font-weight: 600;
            font-size: 16px;
            text-transform: none;
            overflow: hidden;
            text-overflow: ellipsis;
            white-space: nowrap;
            margin-top: 2px;
            text-align: center;
          }

          .name-by {
            margin-top: 2px;
            text-align: center;
            font-weight: 500;
            font-size: 14px;
            color: rgb(112, 122, 131);
          }

          .desc {
            margin: 20px 0px;
            text-align: center;
            display: -webkit-box;
            -webkit-line-clamp: 3;
            -webkit-box-orient: vertical;
            padding: 0 10%;
            font-weight: 400;
            font-size: 16px;
            color: rgb(112, 122, 131);
          }
        }

        .min-item {
          flex: 0 0 200px;
          height: 300px;
          border: 1px solid #e5e8eb;
          background-color: rgb(255, 255, 255);
          border-radius: 10px;
          cursor: pointer;
          margin-bottom: 20px;
          box-shadow: #d8e7f5 0px 3px 10px 0px;

          &:hover {
            transform: translate(0px, -5px);
            transition: 0.1s;
          }

          > img {
            width: 100%;
            object-fit: cover;
            border-top-left-radius: 10px;
            border-top-right-radius: 10px;
          }

          .name {
            color: rgb(4, 17, 29);
            font-weight: 600;
            font-size: 16px;
            text-transform: none;
            overflow: hidden;
            text-overflow: ellipsis;
            white-space: nowrap;
            margin-top: 2px;
            text-align: center;
          }

          .tags {
            background-color: #f7f7f7;

            .tag {
              display: flex;
              padding-left: 5px;
              color:#585656;
              font-weight: 600;
              font-size: 12px;

              > div {
                color: #4caf50;
                font-weight: 500;
              }
            }
          }

          .desc {
            text-align: center;
            display: -webkit-box;
            -webkit-line-clamp: 3;
            -webkit-box-orient: vertical;
            padding: 0 10%;
            font-weight: 400;
            font-size: 16px;
            color: rgb(112, 122, 131);
            margin-top: 10px;

            button {
              width: 100%;
            }
          }
        }
      }
    }
  }
}
.mask {
  background: rgba(0, 0, 0, 0.2);
  position: fixed;
  z-index: 999;
  height: 100vh;
  width: 100vw;
  right: 0;
  display: flex;
  flex-direction: column;
  top: 0;

  .search-form {
    width: 30vw;
    height: 34vw;
    transform: translate(-50%,-50%);
    position: absolute;
    left: 50%;
    top: 50%;
    background: #fff;
    border-radius: 2vw;
    padding: 2vw;

    .title {
      height: 5vw;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 1.6vw;
      font-weight: 500;
    }

    .input {
      width: 100%;
      background: #F0F0F0;
      border: navajowhite;
      line-height: 3;
      border-radius: 10px;
      height: 3vw;
    }

    .common-tips {
      display: flex;
      align-items: center;
      font-size: 16px;
      font-weight: 500;
      margin-top: 0.4vw;

      >img {
        height: 14px;
        width: 14px;
        margin-left: 2px;
      }

    }

    .common {
      display: flex;
      margin-top: 0.4vw;

      .item {
        border: 1px solid #F0F0F0;
        padding: 0px 8px;
        display: flex;
        align-items: center;
        font-size: 16px;
        border-radius: 5px;
        margin-right: 4px;
        font-weight: 500;
        cursor: pointer;

        > img {
          height: 20px;
          width: 20px;
          border-radius: 10px;
          margin-right: 6px;
        }
      }
    }

    .all-ntf {
      display: flex;
      flex-direction: column;
      margin-top: 1vw;
      cursor: pointer;

      .item {
        display: flex;
        align-items: center;
        padding: 5px 0;
        border-bottom: 1px solid #F0F0F0;

        &:hover {
          background: #f9f9f9;
        }

        >img {
          width: 20px;
          height: 20px;
          border-radius: 10px;
          margin-right: 10px;
        }

        .bag {
          .name {
            font-size: 16px;
            font-weight: 600;
          }
          
          .symbol {
            color: #7a7a7a;
            font-size: 12px;
          }
        }
      }

    }
  }

  .x {
    width: 36px;
    height: 36px;
    position: absolute;
    top: 44vw;
    left: 50%;
    font-weight: 500;
    border: 1px solid #ffffff;
    text-align: center;
    border-radius: 50%;
    color: #ffffff;
    font-size: 20px;
    line-height: 36px;
    transform: translate(-50%, -50%);
    cursor: pointer;
  }
}
.drawer {
  .title {
    display: flex;
    align-items: baseline;

    .online {
      margin-right: 8px;

      img {
        height: 30px;
        width: 30px;
        border-radius: 50%;
      }
    }

    a-collapse {
      flex: 1;
      width: 160px;
    }

    > .account {
      flex: 0 0 100px;
      white-space: nowrap;
      text-overflow: ellipsis;
      overflow: hidden;
      font-size: 14px;
      color: #707a83;
      margin-left: auto;
    }
  }

  .money {
    height: 120px;
    border: 1px solid #e5e8eb;
    border-radius: 10px;
    margin-top: 20px;
    overflow: hidden;

    .balance {
      height: 50px;
      font-weight: 500;
      font-size: 14px;
      color: rgb(112, 122, 131);
      display: flex;
      align-items: center;
      justify-content: center;
    }

    .btn {
      height: 70px;
      text-align: center;
      flex: 1;
      background: #2081e2;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 16px;
      font-weight: 600;
      color: #ffffff;
    }
  }
}

.footer {
  display: flex;
  justify-content: center;
  flex-direction: column;
  align-items: center;
  button {
    width: 240px !important;
    margin-bottom: 10px;
  }
}

.normal-btn {
  background-image: linear-gradient(to right, #4DF75D, #0CA4C4);
  border: none  !important;;
  border-radius: 4px !important;;
  box-shadow: rgba(65, 231, 112, 30%) 0px 0px 4px 1px;
  width: 80px !important;
  height: 30px !important;

  &:focus {
    background-image: linear-gradient(to right, #4DF75D, #0CA4C4);
    box-shadow: rgba(65, 231, 112, 30%) 0px 0px 4px 1px;
  }

  &:hover {
    background-image: linear-gradient(to right, #4DF75D, #0CA4C4);
    box-shadow: rgba(65, 231, 112, 30%) 0px 0px 4px 1px;
  }
}

.ant-btn-primary[disabled].normal-btn {
  background: #D6D5D5;
  box-shadow: none !important;
}

.ant-modal-content {
  border-radius: 15px !important;
}

.ant-modal-header {
  background: #0ca4c4 url('../assets/header_bg.png') no-repeat;
  background-size: 100% 100%;
  border-radius: 15px 15px 0 0;
  height: 120px;
  border-bottom: none;
}
.ant-modal-title {
  color: #ffffff;
  text-align: center;
  font-size: 24px;
  margin-top: 20px;
}
.ant-form-item-label {
  display: block;
  width: 80%;
  margin-left: 10%;
  text-align: left;

  label {
    font-weight: 600;
  }
}
.ant-form-item-control-wrapper {
  display: block;
  width: 80%;
  margin-left: 10%;

  input {
    border-radius: 6px;
  }
}
.ant-input:hover ,.ant-input:focus{
  border-color: #25c2a2 !important;
}

.ant-calendar-date {
  border-radius: 50%;
  border: none;
  color: #7C86A2;
  line-height: 24px;
}
.ant-calendar-selected-day .ant-calendar-date {
  background: linear-gradient(45deg, #0CA4C4, #4DF75D);
  color: #ffffff;
}
.ant-calendar-date-panel,.ant-calendar-month-select,.ant-calendar-year-select {
  color:#7C86A2;
}
.ant-calendar-today .ant-calendar-date {
  color: #0fa8bf;
}
.ant-calendar-input {
  color: #7C86A2;
}
.ant-calendar-footer-btn a {
  color: #0fa8bf;
}

.ant-calendar .ant-calendar-ok-btn {
  background: linear-gradient(to right, #4DF75D, #0CA4C4);
  border-radius: 4px !important;;
  box-shadow: rgba(65, 231, 112, 30%) 0px 0px 4px 1px;
  width: 60px !important;
  border: none;
  color: #ffffff;
}
.ant-calendar {
  font-size: 12px;
}
</style>
