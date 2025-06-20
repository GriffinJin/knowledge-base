# Week01

## 2025-06-03

### SCB 参数配置

目标环境：MVP1EPS

参考SQL：

```sql
-- 角色
SELECT * FROM tuap_role_info where lgl_pern_code = '14' and tlr_role_code = '01';
INSERT INTO `tuap_role_info` (`lgl_pern_code`, `tlr_role_code`, `is_warehouse`, `tlr_role_nm`, `tlr_role_desc`, `systemid`, `aplbl_org_scope`, `role_lv_id`, `hndg_alwc_id`, `exclu_role_code`, `auth_alwc_id`, `is_inspector`, `mntnc_tlr`, `mntnc_dt`, `mntnc_tm`, `crea_dt`, `crea_tm`, `mntnc_tmstp`, `rec_ste`) VALUES ('14', '01', '0', 'Super User', 'Super User', 'account,core', '1,2', 2, NULL, '', NULL, NULL, '00068', '20250304', '19:15:38', NULL, NULL, '1741086938281', '0');
-- 机构 Ratchayothin
SELECT * FROM tuap_org_info where lgl_pern_code = '14' and oprtg_org = '0000';
INSERT INTO `tuap_org_info` (`lgl_pern_code`, `oprtg_org`, `br_id`, `org_tp`, `org_chins_nm`, `org_shrt_nm`, `org_engl_nm`, `org_brvt_pinyin`, `rgon_code_num`, `prov_dist_code_num`, `addr`, `engl_addr`, `pstcd`, `tel_num`, `fax_num`, `fincl_lics_num`, `cntct_pern_nm`, `cntct_pern_cntct_tel`, `org_lvl`, `email_addr`, `web_addr`, `enabl_dt`, `org_enabl_ste`, `org_on`, `cfm_acct_ste`, `cfm_acct_dt`, `wthr_newly_added_org`, `sers_num`, `taskgroup`, `seven_twenty_four_hr_flg`, `mntnc_tlr`, `mntnc_dt`, `mntnc_tm`, `crea_dt`, `crea_tm`, `mntnc_tmstp`, `rec_ste`) VALUES ('14', '0000', NULL, '2', 'HP PRODUCT DUMMY BRANCH', '', 'HP PRODUCT DUMMY BRANCH', '', '001', '', '', '', '', '', '', '', '', '', '', '', '', '', '0', '', '', '', '', '', '', '0', NULL, NULL, NULL, NULL, NULL, NULL, NULL);
-- 柜员
select * from tuap_user_info where lgl_pern_code = '14' and user_id = '00068';
select * from tuap_user_run_data where lgl_pern_code = '14' and user_id = '00068';
SELECT * FROM tuap_rolorg_info where lgl_pern_code = '14' and user_id = '00068';
INSERT INTO `tuap_user_info` (`lgl_pern_code`, `user_id`, `user_nm`, `gender`, `birth_dt`, `id_issue_cty`, `docs_catg`, `docs_num`, `oprtg_org`, `tlr_attr`, `tlr_enabl_flg`, `enabl_dt`, `end_dt`, `strt_tm`, `fnis_tm`, `tlr_ste`, `onln_ste`, `tlr_prvg_ste`, `tlr_medm_id`, `email_addr`, `tlr_card_num`, `pswd_mod_dt`, `pswd_vld_dys`, `pswd_cannot_repeat_cnt`, `pswd_alw_err_cnt`, `pswd_err_cnt`, `pswd_ste`, `tlr_pswd`, `pswd_vld_moh`, `profit_centr`, `pswd_repeat_cnt`, `mntnc_tlr`, `mntnc_dt`, `mntnc_tm`, `crea_dt`, `crea_tm`, `mntnc_tmstp`, `rec_ste`, `user_regs_chked`) VALUES ('14', '00068', 'User68', '1', '19900101', '', NULL, NULL, '0000', '0', '1', '19900101', '20990101', '', '', '0', '1', '0', '0', '', NULL, '', NULL, 5, 6, 0, '0', '888888', 3, '', '0', '00068', '20250529', '19:00:35', '20251003', '16:30:15', 1748516435706, '0', '1');
INSERT INTO `tuap_user_run_data` (`user_id`, `lgl_pern_code`, `role_scope`, `txn_oprtg_org`, `empe_num`, `tlr_dly_stlmt_flg`, `tlr_acctg_sys_dly_stlmt_flg`, `tlr_ste`, `logintoken`, `tlr_logon_ste`, `cfm_acct_ste`, `cfm_acct_dt`, `role_lv_id`, `txn_grp_list`, `athrzn_grp_list`, `recheckgroup`, `sign_in_tm`, `sign_in_dt`, `sign_out_tm`, `user_lv`, `user_nm`, `org_lv`, `org_nm`, `posi_nm`, `posi_id`, `roleid`, `mntnc_tlr`, `mntnc_dt`, `mntnc_tm`, `crea_dt`, `crea_tm`, `mntnc_tmstp`, `rec_ste`, `tkn_eff_tm`) VALUES ('00068', '14', NULL, '0000', NULL, NULL, NULL, NULL, 'e9daf838a9dd4afeb9db46a5934871e5', '0', NULL, NULL, 2, NULL, NULL, NULL, 19035, 20251003, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, '00068', '20250529', '19:00:35', NULL, NULL, 1748516435678, '0', NULL);
INSERT INTO `tuap_rolorg_info` (`lgl_pern_code`, `user_id`, `oprtg_org`, `tlr_role_code`, `mntnc_tlr`, `mntnc_dt`, `mntnc_tm`, `crea_dt`, `crea_tm`, `mntnc_tmstp`, `rec_ste`) VALUES ('14', '00068', '0000', '00', NULL, NULL, NULL, NULL, NULL, NULL, NULL);

-- 菜单按钮权限
SELECT * FROM tapp_task_field WHERE txn_code ='ua1070';
-- cmpt_ctrl_seln=1,不可编辑
SELECT a.cmpt_ctrl_seln,a.* FROM tuap_role_btn a WHERE txn_code ='ua1070';
```



### fat1 环境中文检查

检查fat1环境所有没有词根的中文



