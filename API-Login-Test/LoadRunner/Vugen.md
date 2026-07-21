vuser_init()
{
    g_rt_max_overall = 0.0;
    g_rt_max_overall_txn[0] = '\0';
    g_rt_max_overall_status[0] = '\0';
    g_rt_max_overall_address[0] = '\0';
    g_rt_max_overall_city[0] = '\0';
    g_rt_max_overall_state[0] = '\0';
    g_rt_max_overall_zip[0] = '\0';

    g_rt_max_QQ_02_ServiceQualification_ADI = 0.0;

    rt_csv_open_file();

    lr_output_message("RT_CSV_HEADER,Txn,Status,Address,City,State,Zip,ResponseTimeSec");
    rt_csv_write_header();

    lr_output_message("RT_CSV_MAX_HEADER,Scope,Txn,ResponseTimeSec,Status,Address,City,State,Zip");
    lr_output_message("RT_CSV_FILE_PATH,%s", g_rt_csv_path);
    lr_output_message("RT_CSV_FILTERED_FILE_PATH,%s", g_rt_filtered_csv_path);

    return 0;
}
QQ_02_ServiceQualification_ADI()
{
    clock_t sq_adi_start_clock;

    web_set_sockets_option("SSL_VERSION", "TLS1.2");

    web_add_header("Content-Type", "application/json");

    web_reg_find("Search=All",
                 "Text=\"addressId\"",
                 LAST);

    lr_start_transaction("QQ_02_ServiceQualification_ADI");

    sq_adi_start_clock = rt_now_clock();

    web_custom_request("web_custom_request",
        "URL=https://api-servicequal.dummy.example.com",
        "Method=POST",
        "TargetFrame=",
        "Resource=0",
        "Referer=",
        "Snapshot=t2.inf",
        "Body={ ... }",
        LAST);

    lr_end_transaction("QQ_02_ServiceQualification_ADI", LR_AUTO);

    {
        double sq_adi_rt;

        sq_adi_rt = rt_elapsed_sec(sq_adi_start_clock);

        lr_output_message(
            "RT_CSV,%s,%s,%s,%s,%s,%s,%.3f",
            "QQ_02_ServiceQualification_ADI",
            "AUTO",
            lr_eval_string("{address}"),
            lr_eval_string("{city}"),
            lr_eval_string("{state}"),
            lr_eval_string("{zip}"),
            sq_adi_rt);

        rt_csv_write_row(
            "QQ_02_ServiceQualification_ADI",
            "AUTO",
            lr_eval_string("{address}"),
            lr_eval_string("{city}"),
            lr_eval_string("{state}"),
            lr_eval_string("{zip}"),
            sq_adi_rt);

        update_rt_max(
            "QQ_02_ServiceQualification_ADI",
            "AUTO",
            lr_eval_string("{address}"),
            lr_eval_string("{city}"),
            lr_eval_string("{state}"),
            lr_eval_string("{zip}"),
            sq_adi_rt);
    }

    return 0;
}
QQ_04_PriceOffer_ADI()
{
    web_set_sockets_option("SSL_VERSION", "TLS1.2");

    web_add_header("x-client-id", "{ClientID}");
    web_add_header("x-client-secret", "{ClientSecret}");
    web_add_header("x-request-trace-id", "DUMMY_TRACE_001");
    web_add_header("Content-Type", "application/json");

    web_reg_find("Search=All",
                 "Text=\"addressId\"",
                 LAST);

    web_reg_save_param_ex(
        "ParamName=AddressID2",
        "LB=\"addressId\":\"",
        "RB=\"",
        "Ordinal=1",
        SEARCH_FILTERS,
        LAST);

    lr_start_transaction("QQ_02_ServiceQualification_ADI");

    web_custom_request("web_custom_request",
        "URL=https://api-servicequal.dummy.example.com",
        "Method=POST",
        "TargetFrame=",
        "Resource=0",
        "Referer=",
        "Snapshot=t2.inf",
        "Body={ ... }",
        LAST);

    lr_end_transaction("QQ_02_ServiceQualification_ADI", LR_AUTO);

    return 0;
}
