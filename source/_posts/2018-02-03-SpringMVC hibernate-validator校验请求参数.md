---
title: SpringMVC hibernate-validator校验请求参数
date: 2018-02-03 15:04:51
tags:
- spring
categories:
- spring
---

> By default use of @EnableWebMvc or <mvc:annotation-driven> automatically registers Bean Validation support in Spring MVC through the LocalValidatorFactoryBean when a Bean Validation provider such as Hibernate Validator is detected on the classpath.

<!--more-->

> 注意 需要检验的bean需要被 context:component-scan 扫描到

> 如果不指定校验器，classpath内有hibernate validoter，spring会默认使用它。

```java
	@ApiOperation(value="提交商城订单",tags="商城")
	@Auth
	@RequestMapping(value = "/submitMallOrder",method=RequestMethod.POST)
	@ResponseBody
	public Object submitMallOrder(@RequestBody @Valid MallOrderInfosVO mallOrderInfoVO, BindingResult binding) {
		if(binding.hasErrors()){
			return ResultMessage.getValidFail(new HashMap<String,Object>()).setBindingResult(binding);
		}
		Map<String,Object> result = mallOrderInfoService.submitMallOrder(mallOrderInfoVO);
		return ResultMessage.newInstance().setCode(MapUtils.getInteger(result, "code")).setMessage(MapUtils.getString(result, "message")).setData(MapUtils.getObject(result, "data"));
	}
```

```java
package com.jsz.peini.bean.mall;

import java.math.BigDecimal;
import java.util.ArrayList;
import java.util.List;

import javax.validation.Valid;
import javax.validation.constraints.DecimalMin;
import javax.validation.constraints.NotNull;
import javax.validation.constraints.Size;

import org.hibernate.validator.constraints.Range;

import io.swagger.annotations.ApiModelProperty;

public class MallOrderInfosVO {

	@NotNull
	@Size(min=1)
	@Valid
	@ApiModelProperty("商城订单列表")
	public ArrayList<MallSellerOrderInfoVo> sellerOrderInfoVos;
	
	@NotNull
	@DecimalMin("0.00")
	@ApiModelProperty("订单总额")
	public BigDecimal totalMoney;
	
//	@NotNull
//	@Range(min=1,max=3)
//	@ApiModelProperty("支付方式 0金币；1微信；2支付宝")
//	public Integer payType;

	@ApiModelProperty("如果是通过购物车提交的订单，传过来购物车的id")
	public List<Integer> mallShopCartIds;

	@NotNull
	@ApiModelProperty("收货地址id")
	public Integer mallAddressId;

	public ArrayList<MallSellerOrderInfoVo> getSellerOrderInfoVos() {
		return sellerOrderInfoVos;
	}

	public void setSellerOrderInfoVos(ArrayList<MallSellerOrderInfoVo> sellerOrderInfoVos) {
		this.sellerOrderInfoVos = sellerOrderInfoVos;
	}

	public BigDecimal getTotalMoney() {
		return totalMoney;
	}

	public void setTotalMoney(BigDecimal totalMoney) {
		this.totalMoney = totalMoney;
	}

//	public Integer getPayType() {
//		return payType;
//	}
//
//	public void setPayType(Integer payType) {
//		this.payType = payType;
//	}

	public List<Integer> getMallShopCartIds() {
		return mallShopCartIds;
	}

	public void setMallShopCartIds(List<Integer> mallShopCartIds) {
		this.mallShopCartIds = mallShopCartIds;
	}

	public Integer getMallAddressId() {
		return mallAddressId;
	}

	public void setMallAddressId(Integer mallAddressId) {
		this.mallAddressId = mallAddressId;
	}
}

```

```java
package com.jsz.peini.bean.mall;

import java.math.BigDecimal;
import java.util.ArrayList;

import javax.validation.Valid;
import javax.validation.constraints.DecimalMin;
import javax.validation.constraints.Min;
import javax.validation.constraints.NotNull;
import javax.validation.constraints.Size;

import org.hibernate.validator.constraints.Length;
import org.hibernate.validator.constraints.Range;

import io.swagger.annotations.ApiModelProperty;

public class MallSellerOrderInfoVo {

	@NotNull
	@ApiModelProperty("店铺id")
	private Integer sellerId;
	@NotNull
	@DecimalMin("0.00")
	@ApiModelProperty("金额合计")
	private BigDecimal totalMoney;
	@NotNull
	@DecimalMin("0.00")
	@ApiModelProperty("商品总价")
	private BigDecimal goodsMoney;
	@NotNull
	@DecimalMin("0")
	@ApiModelProperty("支付金额")
	private BigDecimal payMoney;
	@NotNull
	@DecimalMin("0")
	@ApiModelProperty("快递费用")
	private BigDecimal expressPrice;
	@NotNull
	@DecimalMin("0")
	@ApiModelProperty("积分补贴")
	private BigDecimal scoreMoney;
	@NotNull
	@DecimalMin("0")
	@ApiModelProperty("积分消耗")
	private Integer scoreUse;
	@NotNull
	@Min(1)
	@ApiModelProperty("购买件数")
	private Integer buyNum;
	
	@NotNull
	@Range(min=0,max=3)
	@ApiModelProperty("发票类型 0:不要发票 1:个人 2:企业 ")
	private Integer invoiceType;
	@Length(max=200)
	@ApiModelProperty("发票抬头")
	private String invoiceTitle;
	@Length(max=200)
	@ApiModelProperty("留言")
	private String leaveMsg;
	
	@NotNull
	@ApiModelProperty("收货人")
	private String receiveUser;
	
	@NotNull
	@ApiModelProperty("收货电话")
	private String receiveTel;
	@Length(max=200)
	@NotNull
	@ApiModelProperty("收货地址")
	private String receiveAddress;
	
	@Size(min=1)
	@ApiModelProperty("商品订单列表")
	@Valid
	private ArrayList<MallGoodsOrderInfoVO> mallGoodsOrderInfoVOs;
	public Integer getSellerId() {
		return sellerId;
	}
	public void setSellerId(Integer sellerId) {
		this.sellerId = sellerId;
	}
	public BigDecimal getTotalMoney() {
		return totalMoney;
	}
	public void setTotalMoney(BigDecimal totalMoney) {
		this.totalMoney = totalMoney;
	}
	public BigDecimal getPayMoney() {
		return payMoney;
	}
	public void setPayMoney(BigDecimal payMoney) {
		this.payMoney = payMoney;
	}
	public BigDecimal getExpressPrice() {
		return expressPrice;
	}
	public void setExpressPrice(BigDecimal expressPrice) {
		this.expressPrice = expressPrice;
	}
	public BigDecimal getScoreMoney() {
		return scoreMoney;
	}
	public void setScoreMoney(BigDecimal scoreMoney) {
		this.scoreMoney = scoreMoney;
	}
	
	public Integer getScoreUse() {
		return scoreUse;
	}
	public void setScoreUse(Integer scoreUse) {
		this.scoreUse = scoreUse;
	}
	public Integer getBuyNum() {
		return buyNum;
	}
	public void setBuyNum(Integer buyNum) {
		this.buyNum = buyNum;
	}
	public Integer getInvoiceType() {
		return invoiceType;
	}
	public void setInvoiceType(Integer invoiceType) {
		this.invoiceType = invoiceType;
	}
	public String getInvoiceTitle() {
		return invoiceTitle;
	}
	public void setInvoiceTitle(String invoiceTitle) {
		this.invoiceTitle = invoiceTitle;
	}
	public String getLeaveMsg() {
		return leaveMsg;
	}
	public void setLeaveMsg(String leaveMsg) {
		this.leaveMsg = leaveMsg;
	}
	public String getReceiveUser() {
		return receiveUser;
	}
	public void setReceiveUser(String receiveUser) {
		this.receiveUser = receiveUser;
	}
	public String getReceiveTel() {
		return receiveTel;
	}
	public void setReceiveTel(String receiveTel) {
		this.receiveTel = receiveTel;
	}
	public String getReceiveAddress() {
		return receiveAddress;
	}
	public void setReceiveAddress(String receiveAddress) {
		this.receiveAddress = receiveAddress;
	}
	public ArrayList<MallGoodsOrderInfoVO> getMallGoodsOrderInfoVOs() {
		return mallGoodsOrderInfoVOs;
	}
	public void setMallGoodsOrderInfoVOs(ArrayList<MallGoodsOrderInfoVO> mallGoodsOrderInfoVOs) {
		this.mallGoodsOrderInfoVOs = mallGoodsOrderInfoVOs;
	}
	public BigDecimal getGoodsMoney() {
		return goodsMoney;
	}
	public void setGoodsMoney(BigDecimal goodsMoney) {
		this.goodsMoney = goodsMoney;
	}
	
	
	
}

```

一个完整实例
http://blog.csdn.net/vbirdbest/article/details/72620957

<blockquote class="blockquote-center">今天最好的表现，是明天最低的要求。</blockquote>