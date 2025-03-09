package de.jonasbark.stripepayment

import com.gettipsi.stripe.StripeModule
import io.flutter.embedding.engine.plugins.FlutterPlugin
import io.flutter.embedding.engine.plugins.activity.ActivityAware
import io.flutter.embedding.engine.plugins.activity.ActivityPluginBinding
import io.flutter.plugin.common.MethodCall
import io.flutter.plugin.common.MethodChannel
import io.flutter.plugin.common.MethodChannel.MethodCallHandler
import io.flutter.plugin.common.MethodChannel.Result

class StripePaymentPlugin : FlutterPlugin, ActivityAware, MethodCallHandler {
    private lateinit var channel: MethodChannel
    private var stripeModule: StripeModule? = null

    override fun onAttachedToEngine(binding: FlutterPlugin.FlutterPluginBinding) {
        channel = MethodChannel(binding.binaryMessenger, "stripe_payment")
        stripeModule = StripeModule(binding.applicationContext)
        channel.setMethodCallHandler(this)
    }

    override fun onDetachedFromEngine(binding: FlutterPlugin.FlutterPluginBinding) {
        channel.setMethodCallHandler(null)
    }

    override fun onAttachedToActivity(binding: ActivityPluginBinding) {
        stripeModule?.setActivity(binding.activity)
    }

    override fun onDetachedFromActivityForConfigChanges() {}

    override fun onReattachedToActivityForConfigChanges(binding: ActivityPluginBinding) {
        stripeModule?.setActivity(binding.activity)
    }

    override fun onDetachedFromActivity() {}

    override fun onMethodCall(call: MethodCall, result: Result) {
        when (call.method) {
            "setOptions" -> {
                val options = call.argument<Map<String, Any>>("options")
                val errorCodes = call.argument<Map<String, Any>>("errorCodes")
                stripeModule?.init(options, errorCodes)
                result.success(null)
            }
            "setStripeAccount" -> {
                stripeModule?.setStripeAccount(call.argument("stripeAccount"))
                result.success(null)
            }
            "deviceSupportsAndroidPay" -> stripeModule?.deviceSupportsAndroidPay(createPromise(result))
            "canMakeAndroidPayPayments" -> stripeModule?.canMakeAndroidPayPayments(createPromise(result))
            "paymentRequestWithAndroidPay" -> stripeModule?.paymentRequestWithAndroidPay(
                call.arguments as Map<String, Any>, createPromise(result)
            )
            "paymentRequestWithCardForm" -> stripeModule?.paymentRequestWithCardForm(
                call.arguments as Map<String, Any>, createPromise(result)
            )
            "createTokenWithCard" -> stripeModule?.createTokenWithCard(
                call.arguments as Map<String, Any>, createPromise(result)
            )
            "createTokenWithBankAccount" -> stripeModule?.createTokenWithBankAccount(
                call.arguments as Map<String, Any>, createPromise(result)
            )
            "createSourceWithParams" -> stripeModule?.createSourceWithParams(
                call.arguments as Map<String, Any>, createPromise(result)
            )
            "createPaymentMethod" -> stripeModule?.createPaymentMethod(
                call.arguments as Map<String, Any>, createPromise(result)
            )
            "authenticatePaymentIntent" -> stripeModule?.authenticatePaymentIntent(
                call.arguments as Map<String, Any>, createPromise(result)
            )
            "confirmPaymentIntent" -> stripeModule?.confirmPaymentIntent(
                call.arguments as Map<String, Any>, createPromise(result)
            )
            "authenticateSetupIntent" -> stripeModule?.authenticateSetupIntent(
                call.arguments as Map<String, Any>, createPromise(result)
            )
            "confirmSetupIntent" -> stripeModule?.confirmSetupIntent(
                call.arguments as Map<String, Any>, createPromise(result)
            )
            else -> result.notImplemented()
        }
    }

    private fun createPromise(result: Result): Promise {
        return object : Promise {
            override fun resolve(value: Any?) {
                result.success(value)
            }

            override fun reject(code: String?, message: String?, throwable: Throwable?) {
                result.error(code ?: "ERROR", message, throwable?.localizedMessage)
            }
        }
    }
}
