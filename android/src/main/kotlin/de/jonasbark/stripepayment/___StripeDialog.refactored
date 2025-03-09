package de.jonasbark.stripepayment

import android.content.DialogInterface
import android.os.Bundle
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import android.widget.Toast
import androidx.fragment.app.DialogFragment
import com.stripe.android.Stripe
import com.stripe.android.model.PaymentMethod
import com.stripe.android.model.PaymentMethodCreateParams
import com.stripe.android.view.CardMultilineWidget
import kotlinx.coroutines.CoroutineScope
import kotlinx.coroutines.Dispatchers
import kotlinx.coroutines.launch

class StripeDialog : DialogFragment() {

    companion object {
        @JvmStatic
        fun newInstance(title: String, stripeAccountId: String?): StripeDialog {
            val frag = StripeDialog()
            val args = Bundle()
            args.putString("stripeAccountId", stripeAccountId)
            args.putString("title", title)
            frag.arguments = args
            return frag
        }
    }

    override fun onCreateView(
        inflater: LayoutInflater, container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        return inflater.inflate(R.layout.activity_stripe, container, false)
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        val title = arguments?.getString("title", "Add Source")
        dialog?.setTitle(title)

        val mCardInputWidget = view.findViewById<CardMultilineWidget>(R.id.card_input_widget)
        mCardInputWidget.setShouldShowPostalCode(false)

        view.findViewById<View>(R.id.buttonSave)?.setOnClickListener {
            getToken()
        }
    }

    override fun onCancel(dialog: DialogInterface) {
        super.onCancel(dialog)
        onCancelListener.onCancel(dialog)
    }

    lateinit var onCancelListener: DialogInterface.OnCancelListener
    lateinit var stripeInstance: Stripe
    var tokenListener: ((PaymentMethod) -> (Unit))? = null

    private fun getToken() {
        val mCardInputWidget = view?.findViewById<CardMultilineWidget>(R.id.card_input_widget)

        if (mCardInputWidget?.validateAllFields() == true) {
            mCardInputWidget.card?.let { card ->
                view?.findViewById<View>(R.id.progress)?.visibility = View.VISIBLE
                view?.findViewById<View>(R.id.buttonSave)?.visibility = View.GONE

                val paymentMethodCreateParams = PaymentMethodCreateParams.create(
                    PaymentMethodCreateParams.Card.create(
                        number = card.number,
                        expiryMonth = card.expiryMonth,
                        expiryYear = card.expiryYear,
                        cvc = card.cvc
                    ),
                    PaymentMethod.BillingDetails.Builder().build()
                )

                CoroutineScope(Dispatchers.Main).launch {
                    try {
                        val result = stripeInstance.createPaymentMethod(paymentMethodCreateParams)
                        view?.findViewById<View>(R.id.progress)?.visibility = View.GONE
                        view?.findViewById<View>(R.id.buttonSave)?.visibility = View.GONE
                        tokenListener?.invoke(result)
                        dismiss()
                    } catch (error: Exception) {
                        view?.findViewById<View>(R.id.progress)?.visibility = View.GONE
                        view?.findViewById<View>(R.id.buttonSave)?.visibility = View.VISIBLE
                        Toast.makeText(requireContext(), error.localizedMessage, Toast.LENGTH_LONG).show()
                    }
                }
            }
        } else {
            Toast.makeText(requireContext(), "The card info you entered is not correct", Toast.LENGTH_LONG).show()
        }
    }
}
