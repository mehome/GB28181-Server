<template>
<div class="container-fluid no-padding">
    <section class="content">
        <div class="nav-tabs-custom col-lg-offset-2 col-lg-8 no-padding">
            <ul class="nav nav-tabs">
                <li class="active"><a href="#base-config" data-toggle="tab">{{logoText}} 信令服务配置</a></li>
                <li @click.prevent="getSMSList"><a href="#sms-config" data-toggle="tab">{{logoText}} 流媒体服务配置</a></li>
            </ul>
            <div class="tab-content">
                <div class="tab-pane  active" id="base-config">
                    <form role="form" class="form-horizontal" autocomplete="off" @submit.prevent="onSubmit">
                        <div :class="['form-group' , {'has-error':  errors.has('Serial')}]">
                            <label for="sip-serial" class="col-sm-4 control-label">SIP ID</label>
                            <div class="col-sm-7">
                                <input id="sip-serial" type="text" class="form-control" name="Serial" data-vv-as="SIP ID" v-validate="'required'" v-model.trim="Serial">
                                <span class="help-block">{{errors.first('Serial')}}</span>
                            </div>
                        </div>
                        <div :class="['form-group' , {'has-error': errors.has('Realm')}]">
                            <label for="sip-realm" class="col-sm-4 control-label">SIP 域</label>
                            <div class="col-sm-7">
                                <input id="sip-realm" type="text" class="form-control" name="Realm" data-vv-as="SIP 域" v-validate="'required'" v-model.trim="Realm">
                                <span class="help-block">{{errors.first('Realm')}}</span>
                            </div>
                        </div>
                        <div :class="['form-group' , {'has-error': errors.has('Host')}]">
                            <label for="sip-host" class="col-sm-4 control-label">SIP Host</label>
                            <div class="col-sm-7">
                                <input type="text" id="sip-host" class="form-control" name="Host" data-vv-as="SIP Host" v-validate="'required'" v-model.trim="Host">
                                <span class="help-block">{{errors.first('Host')}}</span>
                            </div>
                        </div>
                        <div :class="['form-group' , {'has-error': errors.has('Port')}]">
                            <label for="sip-port" class="col-sm-4 control-label">SIP 端口</label>
                            <div class="col-sm-7">
                                <input type="text" id="sip-port" class="form-control" name="Port" data-vv-as="SIP 端口" v-validate="'required|numeric'" v-model.trim="Port">
                                <span class="help-block">{{errors.first('Port')}}</span>
                            </div>
                        </div>
                        <div :class="['form-group' , {'has-error': errors.has('DevicePassword')}]">
                            <label for="sip-dev-pwd" class="col-sm-4 control-label">设备统一接入密码</label>
                            <div class="col-sm-7">
                                <input type="text" id="sip-dev-pwd" class="form-control" name="DevicePassword" data-vv-as="设备统一接入密码" v-model.trim="DevicePassword">
                                <span class="help-block">{{errors.first('DevicePassword')}}</span>
                            </div>
                        </div>
                        <div :class="['form-group' , {'has-error': errors.has('TimeServer')}]">
                            <label for="sip-time-server" class="col-sm-4 control-label">校时源(可选)</label>
                            <div class="col-sm-7">
                                <input type="text" id="sip-time-server" class="form-control" name="TimeServer" data-vv-as="校时源" v-validate="" v-model.trim="TimeServer" placeholder="上级国标编号/NTP">
                                <span class="help-block">{{errors.first('TimeServer')}}</span>
                            </div>
                        </div>
                        <div class="form-group">
                            <label for="black-serial-list" class="col-sm-4 control-label">黑名单 ID(可选)</label>
                            <div class="col-sm-7">
                                <input type="text" id="black-serial-list" class="form-control" name="BlackSerialList" data-vv-as="黑名单 ID" v-model.trim="BlackSerialList" placeholder="选填">
                                <span class="help-block"></span>
                            </div>
                        </div>
                        <div class="form-group">
                            <label for="black-ip-list" class="col-sm-4 control-label">黑名单 IP(可选)</label>
                            <div class="col-sm-7">
                                <input type="text" id="black-ip-list" class="form-control" name="BlackIPList" data-vv-as="黑名单 IP" v-model.trim="BlackIPList" placeholder="选填">
                                <span class="help-block"></span>
                            </div>
                        </div>
                        <div class="form-group">
                            <label for="black-ua-list" class="col-sm-4 control-label">黑名单 UA(可选)</label>
                            <div class="col-sm-7">
                                <input type="text" id="black-ua-list" class="form-control" name="BlackUAList" data-vv-as="黑名单 UA" v-model.trim="BlackUAList" placeholder="选填">
                                <span class="help-block"></span>
                            </div>
                        </div>
                        <div class="form-group">
                            <label class="col-sm-4 control-label">其他配置</label>
                            <div class="col-sm-7 checkbox">
                                <el-checkbox style="margin-left:-19px;margin-top:-5px;" size="small" v-model.trim="APIAuth" name="APIAuth">HTTP 接口鉴权</el-checkbox>
                                &nbsp;&nbsp;&nbsp;&nbsp;
                                <el-checkbox style="margin-left:-19px;margin-top:-5px;" size="small" v-model.trim="SIPLog" name="SIPLog">信令日志</el-checkbox>
                                <span class="help-block"></span>
                            </div>
                        </div>
                        <div class="form-group">
                            <div class="col-sm-offset-4 col-sm-7">
                                <button type="submit" class="btn btn-primary" :disabled="isBasicNoChange || errors.any() || bCommitting">保存</button>
                            </div>
                        </div>
                    </form>
                </div>
                <div class="tab-pane" id="sms-config">
                    <form role="form" class="form-horizontal" autocomplete="off">
                        <div class="form-group" v-if="smss.length > 0">
                            <label class="col-sm-4 control-label">SMS 服务</label>
                            <div class="col-sm-7">
                                <select class="form-control" v-model.trim="smsserial" @change="smschange">
                                    <option v-for="(c, idx) in smss" :value="c.Serial" :key="idx">{{c.Serial}}</option>
                                </select>
                            </div>
                        </div>
                        <div class="form-group" v-if="smss.length <= 0">
                            <div class="col-sm-12">
                                <div class="alert text-center no-margin">SMS 流媒体服务尚未启动</div>
                            </div>
                        </div>
                    </form>
                    <form v-if="smsbaseconfig.Host != undefined && smss.length > 0" role="form" class="form-horizontal" autocomplete="off" @submit.prevent="onSubmitSMS">
                        <div :class="['form-group' , {'has-error': errors.has('smsSerial')}]">
                            <label class="col-sm-4 control-label">SIP ID</label>
                            <div class="col-sm-7">
                                <input type="text" class="form-control" name="smsSerial" data-vv-as="SIP ID" v-validate="'required'" v-model.trim="smsbaseconfig.Serial">
                                <span class="help-block">{{errors.first('smsSerial')}}</span>
                            </div>
                        </div>
                        <div :class="['form-group' , {'has-error': errors.has('smsRealm')}]">
                            <label class="col-sm-4 control-label">SIP 域</label>
                            <div class="col-sm-7">
                                <input type="text" class="form-control" name="smsRealm" data-vv-as="SIP 域" v-validate="'required'" v-model.trim="smsbaseconfig.Realm">
                                <span class="help-block">{{errors.first('smsRealm')}}</span>
                            </div>
                        </div>
                        <div :class="['form-group' , {'has-error': errors.has('smsHost')}]">
                            <label class="col-sm-4 control-label">SIP Host</label>
                            <div class="col-sm-7">
                                <input type="text" class="form-control" name="smsHost" data-vv-as="SIP Host" v-validate="'required'" v-model.trim="smsbaseconfig.Host">
                                <span class="help-block">{{errors.first('smsHost')}}</span>
                            </div>
                        </div>
                        <div :class="['form-group' , {'has-error': errors.has('smsPort')}]">
                            <label class="col-sm-4 control-label">SIP 端口</label>
                            <div class="col-sm-7">
                                <input type="text" class="form-control" name="smsPort" data-vv-as="SIP 端口" v-validate="'required|numeric'" v-model.trim="smsbaseconfig.Port">
                                <span class="help-block">{{errors.first('smsPort')}}</span>
                            </div>
                        </div>
                        <div class="form-group">
                            <label class="col-sm-4 control-label">RTSP 端口(可选)</label>
                            <div class="col-sm-7">
                                <input type="text" class="form-control" name="RTSPPort" data-vv-as="RTSP 端口" v-validate="'numeric'" v-model.trim="smsbaseconfig.RTSPPort" placeholder="选填">
                                <span class="help-block">{{errors.first('RTSPPort')}}</span>
                            </div>
                        </div>
                        <div :class="['form-group' , {'has-error': errors.has('RTMPPort')}]">
                            <label class="col-sm-4 control-label">RTMP 端口</label>
                            <div class="col-sm-7">
                                <input type="text" class="form-control" name="RTMPPort" data-vv-as="RTMP 端口" v-validate="'required|numeric'" v-model.trim="smsbaseconfig.RTMPPort">
                                <span class="help-block">{{errors.first('RTMPPort')}}</span>
                            </div>
                        </div>
                        <div :class="['form-group' , {'has-error': errors.has('RecordDir')}]">
                            <label class="col-sm-4 control-label">云录像目录</label>
                            <div class="col-sm-7">
                                <input type="text" class="form-control" name="RecordDir" data-vv-as="云录像目录" v-model.trim="smsbaseconfig.RecordDir">
                                <span class="help-block">{{errors.first('RecordDir')}}</span>
                            </div>
                        </div>
                        <div :class="['form-group' , {'has-error': errors.has('WanIP')}]">
                            <label class="col-sm-4 control-label">外网 IP(可选)</label>
                            <div class="col-sm-7">
                                <input type="text" class="form-control" name="WanIP" data-vv-as="外网 IP" v-model.trim="smsbaseconfig.WanIP" v-validate="'url'" placeholder="选填">
                                <!-- <span class="help-block">{{errors.first('WanIP')}}</span> -->
                            </div>
                        </div>
                        <div class="form-group">
                            <label class="col-sm-4 control-label">其他配置</label>
                            <div class="col-sm-7 checkbox">
                                <el-checkbox style="margin-left:-19px;margin-top:-5px;" size="small" v-model.trim="smsbaseconfig.GOPCache" name="GOPCache">直播秒开</el-checkbox>
                                &nbsp;&nbsp;&nbsp;&nbsp;
                                <el-checkbox style="margin-left:-19px;margin-top:-5px;" size="small" v-model.trim="smsbaseconfig.HLS" name="HLS">HLS</el-checkbox>
                                &nbsp;&nbsp;&nbsp;&nbsp;
                                <el-checkbox style="margin-left:-19px;margin-top:-5px;" size="small" v-model.trim="smsbaseconfig.SIPLog" name="SIPLog">信令日志</el-checkbox>
                                &nbsp;&nbsp;&nbsp;&nbsp;
                                <el-checkbox style="margin-left:-19px;margin-top:-5px;" size="small" v-model.trim="smsbaseconfig.UseWanIPRecvStream" name="UseWanIPRecvStream">外网 IP 收流</el-checkbox>
                            </div>
                        </div>
                        <div class="form-group">
                            <div class="col-sm-offset-4 col-sm-7">
                                <button type="submit" class="btn btn-primary" :disabled="isSMSNoChange || errors.any() || bCommitting">保存</button>
                            </div>
                        </div>
                    </form>
                </div>
            </div>
            <!-- /.tab-content -->
        </div>
    </section>
</div>
</template>

<script>
import $ from "jquery";
import {
    mapState,
    mapActions
} from "vuex"

export default {
    data() {
        return {
            bCommitting: false,
            Serial: "",
            Realm: "",
            Host: "",
            Port: 0,
            DevicePassword: "",
            TimeServer: "",
            AckTimeout: 0,
            KeepaliveTimeout: 0,
            APIAuth: false,
            SIPLog: false,
            BlackSerialList: "",
            BlackIPList: "",
            BlackUAList: "",
            remoteBasicData: "",
            remoteSMSData: "",
            smsserial: "",
            smss: [],
            sms: {},
            smsbaseconfig: {},
        };
    },
    mounted() {
        this.getBaseConfig()
    },
    methods: {
        async onSubmit() {
            var ok = await this.$validator.validateAll();
            if (!ok) {
                var e = this.errors.items[0];
                this.$message({
                    type: "error",
                    message: e.msg
                });
                $(`[name=${e.field}]`).focus();
                return;
            }
            this.bCommitting = true;
            $.get("/api/v1/setbaseconfig", this.getBasicCommitObject()).then(data => {
                this.$message({
                    type: "success",
                    message: "配置成功！"
                });
            }).always(() => {
                this.getBaseConfig()
                this.bCommitting = false;
            })
        },
        async onSubmitSMS() {
            var ok = await this.$validator.validateAll();
            if (!ok) {
                var e = this.errors.items[0];
                this.$message({
                    type: "error",
                    message: e.msg
                });
                $(`[name=${e.field}]`).focus();
                return;
            }
            this.bCommitting = true;
            $.get("/api/v1/sms/setbaseconfig", this.smsbaseconfig).then(data => {
                this.$message({
                    type: "success",
                    message: "配置成功！"
                });
            }).always(() => {
                this.smsserial = "";
                this.getSMSList()
                this.bCommitting = false;
            })
        },
        getBasicCommitObject() {
            return {
                Serial: this.Serial,
                Realm: this.Realm,
                Host: this.Host,
                Port: this.Port,
                DevicePassword: this.DevicePassword,
                TimeServer: this.TimeServer,
                AckTimeout: this.AckTimeout,
                KeepaliveTimeout: this.KeepaliveTimeout,
                APIAuth: this.APIAuth,
                SIPLog: this.SIPLog,
                BlackSerialList: this.BlackSerialList,
                BlackIPList: this.BlackIPList,
                BlackUAList: this.BlackUAList,
            };
        },
        getBaseConfig() {
            $.get("/api/v1/getbaseconfig").then(ret => {
                this.Serial = ret.Serial;
                this.Realm = ret.Realm;
                this.Host = ret.Host;
                this.Port = ret.Port;
                this.DevicePassword = ret.DevicePassword;
                this.TimeServer = ret.TimeServer;
                this.AckTimeout = ret.AckTimeout;
                this.KeepaliveTimeout = ret.KeepaliveTimeout;
                this.APIAuth = ret.APIAuth;
                this.SIPLog = ret.SIPLog;
                this.BlackSerialList = ret.BlackSerialList;
                this.BlackIPList = ret.BlackIPList;
                this.BlackUAList = ret.BlackUAList;

                this.remoteBasicData = JSON.stringify(this.getBasicCommitObject());
            });
        },
        getSMSList() {
            if (this.smsserial == "") {
                $.get("/api/v1/sms/list").then(ret => {
                    this.smss = ret
                    if (ret.length > 0) {
                        this.sms = ret[0]
                        this.smsserial = ret[0].Serial
                    }
                    this.getSMSInfo();
                })
            }
        },
        getSMSInfo() {
            if (this.smsserial != "") {
                $.get("/api/v1/sms/getbaseconfig", {
                    serial: this.smsserial
                }).then(ret => {
                    this.smsbaseconfig = ret;

                    this.smsbaseconfig["PreSerial"] = this.smsserial;
                    this.remoteSMSData = JSON.stringify(this.smsbaseconfig);
                })
            }
        },
        smschange() {
            this.getSMSInfo()
        }
    },
    computed: {
        ...mapState([
            "logoText",
            "logoMiniText",
            "menus",
            "serverInfo"
        ]),
        isBasicNoChange() {
            var localeData = JSON.stringify(this.getBasicCommitObject());
            return this.remoteBasicData.localeCompare(localeData) == 0;
        },
        isSMSNoChange() {
            this.smsbaseconfig["PreSerial"] = this.smsserial;
            var localeData = JSON.stringify(this.smsbaseconfig);
            return this.remoteSMSData.localeCompare(localeData) == 0;
        }
    }
};
</script>
